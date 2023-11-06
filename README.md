## Priprema za deploy na Render:
- po potrebi dodati env. varijable u run konfiguraciju vašeg IDE-a
- dodati Dockerfile - u direktoriju docker postoje dvije verzije, za Maven i Gradle. Ako se mijenja lokacija Dockerfile-a paziti na putanje unutar COPY naredbi u Dockerfile skripti
- preporuča se u application.properties postaviti property server.servlet.context-path na /api kao prefiks svim zahtjevima na backend
- (opcionalno) u application.properties dodati željene env. varijable u formatu:
  some.property=${ENV_VAR_NAME:default value}
  - primjer u ovome repozitoriju (src/main/resources)
- (opcionalno) u pom.xml kao dependency dodati spring-boot-starter-actuator (prema primjeru u ovom repozitoriju) koji ce na putanju /actuator/health automatski izloziti informaciju o statusu aplikacije koju moze koristiti Render (health check kasnije u uputama)

## Deploy:
### Kreiranje baze podataka:
U Render dashboardu:
- New -> PostgreSQL
- Postaviti ime baze i opcionalno username za korisnika baze (password je automatski generiran)
- Region Frankfurt
- Create Database
- Kreiranje baze podataka može trajati par minuta
- Free plan baza podataka ima max. pohranu od 1GB, te se baza briše nakon 90 dana.

### Kreiranje backenda:
U Render dashboardu:
- New -> Web Service
- Povezati GitHub račun, nakon čega su za odabir dostupni svi projekti na koje imate prava pristupa
- Stisnuti connect pored odgovarajućeg projekta
- Postaviti ime za servis (postat ce dio web adrese)
- Root directory ostaviti prazan
- Environment Docker
- Region Frankfurt
- Na dnu proširiti _advanced_
- Dodati potrebne environment varijable (npr. DB username, password, URL), kopirati vrijednosti iz postavki baze podataka na Renderu. Pripaziti jer URL koji je prikazan na Renderu nije JDBC URL, za ovaj primjer treba biti u formatu `jdbc:postgresql://hostname:port/database`
- Ako je dodan Spring Boot Actuator (prema zadnjoj točki poglavlja pripreme za deploy) postaviti `/api/actuator/health` kao Health Check Path (odnosno <context-path>/actuator/health)
- Postaviti putanju za Dockerfile ovisno koji se package manager koristi (u ovom slučaju `./docker/maven/Dockerfile`)
- Stisnuti Create Web Service

Napomena: U free tier-u nakon perioda neaktivnosti aplikacije na Renderu bit će automatski ugašene, te ponovno podignute pri prvom zaprimanju zatjeva (otvaranje web stranice frontenda ili slanje zahtjeva na API).
Ovo može rezultirati s nekoliko minuta nedostupnosti aplikacije ako se otvara npr. prvi put u danu.