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
- Zaimportuj certyfikat Burp'a do swojej przeglądarki. Ustawienia -> Certyfikaty -> Wyświetl certyfikaty... -> Zakładka "Organy certyfikacji" -> Importuj... -> Wybierz pobrany certyfikat -> Zaufaj temu CA przy identyfikacji witryn internetowych -> OK
- Zweryfikuj działanie Burp'a: Wejdź na dowolną stronę w swojej przeglądarce. W Burp w zakładce Proxy -> HTTP history powinny być widoczne przesłane zapytania
### Dostęp do labów
- Załóż konto na stronie [PortSwigger](https://portswigger.net/)
- Po założeniu konta, zaloguj się - wykorzystaj do tego **przeglądarkę ze skonfigurowanym proxy!**

## Laboratoria z wykorzystania SSRF
### Przeanalizuj instrukcję podaną do laboratorium oraz kliknij "ACCESS THE LAB". W każdym labie znajduje się gotowy Solution, ale użyj go <ins>w ostateczności.</ins> 🥺 Próbuj realizować laboratoria samodzielnie z dostępem do zapewnionych podpowiedzi.
### [Lab nr 1](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)
<details>
    <summary>Podpowiedź nr 1</summary>

    Wykorzystaj "HTTP history" lub "Intercept" w Burp'ie do analizy przesyłanych żądań oraz "Repeater" do modyfikacji żądania i przesyłania go do serwera
</details>
<details>
    <summary>Podpowiedź nr 2</summary>

    Znajdź na stronie wspomniany w instrukcji "stock check feature". Jak wygląda to żądanie? Co jest jego parametrem?
    W Repeaterze na zaznaczonym fragmencie żądania:
    CTRL+U - URL-encoding
    CTRL+SHIFT+U - URL-decoding
</details>

---
### [Lab nr 2](https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter)

<details>
    <summary>Podpowiedź nr 1</summary>

    Przeanalizuj przechwycone żądania w Burpie, zgodnie z instrukcją do laboratorium, podobnie jak w Lab nr 1
</details>
<details>
    <summary>Podpowiedź nr 2</summary>

    Po zmianie parametru "stockApi" na http://localhost lub http://127.0.0.1 żądanie jest blokowane. Są to wspomniane w zadaniu słabe zabezpieczenia przed SSRF. Na jakiej zasadzie mogą działać? Czy istnieją alternatywne reprezentacje adresu pętli zwrotnej lub inne nazwy hostów wskazujące na ten sam serwer?
</details>
<details>
    <summary>Podpowiedź nr 3</summary>

    Po pomyślnym odwołaniu się do pętli zwrotnej serwera, próbujemy przejść do admin-panelu. Adres http://[...]/admin zostaje zablokowany. Jest to kolejny rodzaj zabezpieczenia. Czy można w jakiś sposób zaciemnić nasz przekazywany parametr, żeby obejść to proste zabezpieczenie? Kodowanie znaków?
</details>

---
### [Lab nr 3](https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-open-redirection)
<details>
    <summary>Podpowiedź nr 1</summary>

    Przeanalizuj przechwycone żądania w Burpie, zgodnie z instrukcją do laboratorium, podobnie jak w Lab nr 1. Zmiana "stockApi" nie pozwala nam w prosty sposób na zmuszenie serwera do wysłania żądania do innego hosta. Czy na stronie produktu znajdują się jeszcze jakieś funkcje, które mogłyby być luką w zabezpieczeniach?
</details>
<details>
    <summary>Podpowiedź nr 2</summary>

    Na stronie produktu kliknij "Next product" w prawym dolnym rogu. Przeanalizuj przekazane żądania. Jakie parametry są przekazywane w tych żądaniach? Czy możemy je w jakiś sposób wykorzystać do ataku SSRF? Zwróć uwagę, czy paramter "path" przyjmuje tylko relatywne ścieżki, czy może pełne adresy URL?
</details>
<details>
    <summary>Podpowiedź nr 3</summary>

    Porównaj konstrukcję parametru "stockApi" z "check stock feature" i parametr "path" w żądaniu na /product/nextProduct?path=[...]. Jak można to wykorzystać, aby uzyskać dostęp do admin panelu?
</details>

---
### [Lab nr 4](https://portswigger.net/web-security/ssrf/lab-ssrf-with-whitelist-filter)
<details>
    <summary>Podpowiedź nr 1</summary>

    Przeanalizuj przechwycone żądania w Burpie, zgodnie z instrukcją do laboratorium. Podobnie jak w Lab nr 2 żądania z parametrem "stockApi" ustawionym na http://127.0.0.1 lub http://localhost są blokowane. Jaka jest odpowiedź serwera na takie żądania? Co ujawnia nam ten komunikat?
</details>
<details>
    <summary>Podpowiedź nr 2</summary>

    URL-embedded credentials, działanie znaku # w adresie URL i URL double encoding (patrz Lab nr 2)
</details>
<details>
    <summary>Podpowiedź nr 3</summary>

    Whitelista wymaga, aby w adresie pojawiła się domena "stock.weliketoshop.net". Spróbuj skonstruować URL tak, aby to "localhost" był hostem (celem żądania), a wymagana przez filtr domena znalazła się w sekcji danych uwierzytelniających (np. http://localhost@wymagana_domena) lub została w inny sposób "doklejona" do adresu. Sprawdź, jak parser interpretuje taki adres - czy łączy się z localhostem, czy z domeną po znaku @?
</details>
<details>
    <summary>Podpowiedź nr 4</summary>

    Samo użycie @ może sprawić, że serwer połączy się z domeną po prawej stronie tego znaku, a nie z localhostem. Musimy "uciąć" adres przed @. Tu z pomocą przychodzi znak # (kotwica/fragment), po którym reszta adresu jest zazwyczaj ignorowana przez serwer wykonujący żądanie. Problem w tym, że filtr może odrzucić jawny znak #. Jak możemy go zakodować, aby przetrwał weryfikację whitelisty i został zinterpretowany jako # dopiero w momencie wykonywania żądania HTTP? (Pamiętaj o URL-encoding).
</details>