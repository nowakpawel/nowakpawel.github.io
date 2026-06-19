---
layout: post
date: 2026-06-14
categories: [cybersecurity, devsecops]
tags: [github-actions, sast, sca, trivy, semgrep, owasp, devsecops]
title: Budowa pipeline DevSecOps
---
W tym poście opisuję budowę pipeline bezpieczeństwa, który zbudowałem. Chciałbym, aby ten post dokumentował jak uczę się podejścia 
do wytwarzania bezpiecznego kodu w praktyce.


## Co zbudowałem
Przy każdym pushu do brancha main, Github Actions uruchamia trzy narzędzia bezpieczeństwa:
- **SAST** (Semgrep) - analiza kodu Java pod kątem podatności. Kod analizowany równolegle z buildem, co ilustruje poniższy screen z mojego repozytorium.
- **SCA** (OWASP Dependency Check) - skanowanie zależności Maven w poszukiwaniu CVE .
- **Trivy** (Container Scanning)- Skanowanie obrazu Docker przed deployem.

[SCREEN  z Github Actions]
**SAST** nie potrzebuje skompilowanej aplikacji, więc działa równlolegle z buildem, co pozwala na zaoszczędzenie czasu oraz zasobów. 
**SCA** oraz **Trivy** korzystają ze zbudowanej aplikacji. Dlatego uruchamiają się po zakończonym jobie `build`.


## Podatność CVE-2018-1258
OWASP Dependency Check oznaczył `spring-security-web:7.1.0` jako podatny. W Bazie NVD [podatność CVE-2018-1258](https://nvd.nist.gov/vuln/detail/CVE-2018-1258) 
ocena CVSS = 8.8. Czyli HIGH!

### Suppresja
Dalsza analiza wykazała jednak, ze jest to false positive. Baza NVD, w sekcji *Known Affected Software Configurations* mówi, że aby podatność wystąpiła (warunek `AND`)
spring_security:* (dowolna wersja) musi działać w połączeniu ze spring_framework:5.0.5 
Mój projekt używa Spring Framework 7.0.8, więc warunek `AND` nie jest spełniony. Z tego co wyczytałem, to **SCA** działa w sposób heurystyczny, dopasowując CPE z bazy NVD do nazw bibliotek.
Nie sprawdza złożonych warunków, a każdy 'finding' wymaga manualnej weryfikacji.

## Udokumentowana decyzja
W [repozytorium](https://github.com/nowakpawel/devsecops-demo) udokumentowałem decyzję o 'suppresji' w pliku `suppression.xml`. Po Ponownym buildzie i przejściu przez wszystkie pipeliny, błąd zniknął. 
Zawartość pliku `suppress.xml`:
```xml
<suppress>
  <notes>
    CVE-2018-1258 requires Spring Framework 5.0.5 AND any Spring Security version.
    This project uses Spring Framework 7.0.8 — the AND condition is not met.
    False positive.
  </notes>
  <cve>CVE-2018-1258</cve>
</suppress>
```
Plik jest w głównym katalogu projektu. Dzięki niemu można łatwo podejrzeć nie tylko co zostało zsuppressowane, ale też dlaczego.
