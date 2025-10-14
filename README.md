

## Sammendrag
Dette prosjektet er en sikkerhetsøvelse som simulerer vanlige angrepsvektorer innen penetrasjonstesting, pivoting og privilege escalation. Dokumentasjonen viser, steg for steg i et kontrollert labmiljø, hvordan en sårbar Windows-maskin kan utnyttes (Nmap, Metasploit, Meterpreter) for å få tilgang, samle ukryptert nettverkstrafikk (netsh → ETL → PCAP) og analysere den i Wireshark. Øvelsen demonstrerer også hvordan en angriper kan dekke spor og eskalere tilgang, og har som formål å gi opplæring i hvordan slike hendelser oppdages og håndteres — dette er ikke et reelt oppdrag, men en simulert test.

## DEL A
I første del av oppgaven kjører vi en nmap-scan på Windows-maskinen for å se om denne er sårbar ovenfor EternalBlue-sårbarheten. Dette gjør vi ved å kjøre en nmap-kommando egnet for denne sårbarheten:<img width="634" height="46" alt="image" src="https://github.com/user-attachments/assets/d88a936b-9cd9-4512-a1a4-ef75649d256a" />

Scannen bekreftet at denne maskinen er sårbar for EternalBlue-sårbarheten og at det er høy risk for at det kan utføres en remote execution på denne maskinen. Dette gjør vi via Metasploit i neste steg. <img width="871" height="197" alt="image" src="https://github.com/user-attachments/assets/a4ff730f-9e33-4d46-ad00-a3889dbd9f1a" />

Nå som sårbarheten er bekreftet, kan du sette opp en remote execution ved hjelp av Metasploit-verktøyet. Vi starter opp Metasplot med «msfconsole» kommando: <img width="233" height="47" alt="image" src="https://github.com/user-attachments/assets/1c53b8c1-0a6e-44b8-882c-f6af88e3e441" />

På Metasploit finnes det forskjellige moduler for denne sårbarheten. For å få listet dette opp skriver vi «search ms17-010». Dette resulterte i flere valg, blant annet remote exection for EternalBlue-sårbarheten. Vi tar i bruk denne ved skrive «use 0»: <img width="940" height="118" alt="image" src="https://github.com/user-attachments/assets/771213ff-ba7d-4edd-af3a-d143115f27f4" />

Når modulen er valgt må vi sette oppgi offermaskinens IP-adresse:<img width="760" height="40" alt="image" src="https://github.com/user-attachments/assets/4585d6b8-83dc-4a03-8adc-fc86b9c9e6b8" /> <img width="754" height="40" alt="image" src="https://github.com/user-attachments/assets/b651bef4-a131-4ef6-b400-fea665f02ce7" />

