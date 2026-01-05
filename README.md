# SSRF - Server-Side Request Forgery
## Przygotowanie do ćwiczeń
### Instalacja Burp
- Do wykonania ćwiczeń potrzebny jest program Burp Suite Community Edition dostępny do pobrania z tej [strony](https://portswigger.net/burp/releases/professional-community-2025-11-6?requestededition=community&requestedplatform=)
- Po instalacji programu tworzymy Temporary project in memory -> Next -> Use Burp Defaults -> Start Burp
### Konfiguracja wybranej przeglądarki (Na przykładzie Firefox'a)
- Następnie należy skonfigurować przeglądarkę, aby ruch z przeglądarki był kierowany przez Proxy Listener Burp'a
- Zweryfikuj adres na którym nasłuchuje burp: Zakładka Proxy -> Proxy settings -> Proxy listeners (domyślnie 127.0.0.1:8080)
- W ustawieniach wybranej przeglądarki skonfiguruj ustawienia proxy: Ustawienia -> Sieć -> Ustawienia... -> Wybierz opcję "Ręczna konfiguracja serwerów proxy" -> Jako serwer proxy HTTP wprowadź adres widoczny w sekcji "Proxy listeners"
- Zaznacz opcję "Użyj tego serwera proxy także dla HTTPS"
- Przejdź do strony http://burpsuite/ i kliknij "CA Certificate" w prawym górnym rogu - pobrany zostanie certyfikat
- Zaimportuj certyfikat Burp'a do swojej przeglądarki. Na przykładzie firefoxa: Ustawienia -> Certyfikaty -> Wyświetl certyfikaty... -> Zakładka "Organy certyfikacji" -> Importuj... -> Wybierz pobrany certyfikat -> Zaufaj temu CA przy identyfikacji witryn internetowych -> OK
- Zweryfikuj działanie Burp'a: Wejdź na dowolną stronę w swojej przeglądarce. W Burp w zakładce Proxy -> HTTP history powinny być widoczne przesłane zapytania
### Dostęp do labów
- Załóż konto na stronie [PortSwigger](https://portswigger.net/)
- Po założeniu konta, zaloguj się - wykorzystaj do tego **przeglądarkę ze skonfigurowanym proxy!**

## Laboratoria z wykorzystania SSRF
### [Lab nr 1](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)
#### Przeanalizuj instrukcję podaną do laboratorium oraz kliknij "ACCESS THE LAB"
<details>
    <summary>Podpowiedź nr 1</summary>

    Wykorzystaj "HTTP history" lub "Interceptor" w Burp'ie do analizy przesyłanych żądań oraz "Repeater" do modyfikacji żądania i przesyłania go do serwera
</details>
<details>
    <summary>Podpowiedź nr 2</summary>

    Znajdź na stronie wspomniany w instrukcji "stock check feature". Jak wygląda to żądanie? Co jest jego parametrem?
    W Repeaterze na zaznaczonym fragmencie żądania:
    CTRL+U - URL-encoding
    CTRL+SHIFT+U - URL-decoding
</details>