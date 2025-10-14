

## Sammendrag
Dette prosjektet er en sikkerhetsøvelse som simulerer vanlige angrepsvektorer innen penetrasjonstesting, pivoting og privilege escalation. Dokumentasjonen viser, steg for steg i et kontrollert labmiljø, hvordan en sårbar Windows-maskin kan utnyttes (Nmap, Metasploit, Meterpreter) for å få tilgang, samle ukryptert nettverkstrafikk (netsh → ETL → PCAP) og analysere den i Wireshark. Øvelsen demonstrerer også hvordan en angriper kan dekke spor og eskalere tilgang, og har som formål å gi opplæring i hvordan slike hendelser oppdages og håndteres — dette er ikke et reelt oppdrag, men en simulert test.

## DEL A NMAP & Metasploit
I første del av oppgaven kjører vi en nmap-scan på Windows-maskinen for å se om denne er sårbar ovenfor EternalBlue-sårbarheten. Dette gjør vi ved å kjøre en nmap-kommando egnet for denne sårbarheten:<img width="634" height="46" alt="image" src="https://github.com/user-attachments/assets/d88a936b-9cd9-4512-a1a4-ef75649d256a" />

Scannen bekreftet at denne maskinen er sårbar for EternalBlue-sårbarheten og at det er høy risk for at det kan utføres en remote execution på denne maskinen. Dette gjør vi via Metasploit i neste steg. <img width="871" height="197" alt="image" src="https://github.com/user-attachments/assets/a4ff730f-9e33-4d46-ad00-a3889dbd9f1a" />

Nå som sårbarheten er bekreftet, kan du sette opp en remote execution ved hjelp av Metasploit-verktøyet. Vi starter opp Metasplot med «msfconsole» kommando: <img width="233" height="47" alt="image" src="https://github.com/user-attachments/assets/1c53b8c1-0a6e-44b8-882c-f6af88e3e441" />

På Metasploit finnes det forskjellige moduler for denne sårbarheten. For å få listet dette opp skriver vi «search ms17-010». Dette resulterte i flere valg, blant annet remote exection for EternalBlue-sårbarheten. Vi tar i bruk denne ved skrive «use 0»: <img width="940" height="118" alt="image" src="https://github.com/user-attachments/assets/771213ff-ba7d-4edd-af3a-d143115f27f4" />

Når modulen er valgt må vi sette oppgi offermaskinens IP-adresse:<img width="760" height="40" alt="image" src="https://github.com/user-attachments/assets/4585d6b8-83dc-4a03-8adc-fc86b9c9e6b8" /> <img width="754" height="40" alt="image" src="https://github.com/user-attachments/assets/b651bef4-a131-4ef6-b400-fea665f02ce7" />

Deretter setter vi Kali maskinen vår som LHOST for at offermaskinen (Windows) og Kali maskinen skal kommuniserer. Dette er viktig i en remote execution for at offermaskinen skal vite hvor den skal koble seg tilbake. Samme gjelder port, men i dette tilfelle vet vi at den allerede tar i bruk port 445. Hadde den brukt noe annet enn standardporten for SMB, ville det være nødvendig å spesifisere det.
Videre må vi sette payload og den vi velger er Meterpreter reverse shell: <img width="940" height="39" alt="image" src="https://github.com/user-attachments/assets/ab60a2e9-6505-421d-b5bc-31474c0e20d0" />

Når alt er satt kan vi starte angrepet ved å skrive «exploit»:
<img width="631" height="438" alt="image" src="https://github.com/user-attachments/assets/b15f3bd4-e6dc-4f6b-882f-15ad799c362b" />
Her får vi bekreftet at vår remote execution var vellykket. Vi kan nå gå videre til Del B av angrepet.

## DEL B Packet Capture
Første må vi starte en shell ved å skrive «shell» <img width="538" height="146" alt="image" src="https://github.com/user-attachments/assets/319ba00a-6c14-458d-86c1-46c1f823f162" />

Nå som vi har fått tilgang skal vi gjennomføre en packet capture på nettverket. Vi skal bruke verktøyet «netsh». Dette er et legitimt Windows-verktøy som allerede er i maskinen. Her unngår vi å laste ned programmer som Wireshark, hvor installasjonen ville trigget SOC. Vi setter i gang packet capture ved å skrive følgende kommando: «netsh trace start capture=yes tracefile=c:\windows\temp\ntwrkcap.etl»
Denne kommandoen vil lagre nettverkstrafikken vi tar opp i en etl-fil på et vanlig sted som C:\windows\temp\. Her bruker vi en stealth-teknikk for å få det til å se ut som vanlig aktivitet på maskinen. 

Nå som nettverkstrafikken blir tatt opp kan vi starte på Del C av oppgaven. <img width="940" height="241" alt="image" src="https://github.com/user-attachments/assets/67652d75-a428-4ea0-90e3-35c083971654" />
