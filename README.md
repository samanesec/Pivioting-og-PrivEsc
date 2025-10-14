

## Sammendrag
Dette prosjektet er en sikkerhetsøvelse som simulerer vanlige angrepsvektorer innen penetrasjonstesting, pivoting og privilege escalation. Dokumentasjonen viser, steg for steg i et kontrollert labmiljø, hvordan en sårbar Windows-maskin kan utnyttes (Nmap, Metasploit, Meterpreter) for å få tilgang, samle ukryptert nettverkstrafikk (netsh → ETL → PCAP) og analysere den i Wireshark. Øvelsen demonstrerer også hvordan en angriper kan dekke spor og eskalere tilgang, og har som formål å gi opplæring i hvordan slike hendelser oppdages og håndteres — dette er ikke et reelt oppdrag, men en simulert test.

## DEL A
I første del av oppgaven kjører vi en nmap-scan på Windows-maskinen for å se om denne er sårbar ovenfor EternalBlue-sårbarheten. Dette gjør vi ved å kjøre en nmap-kommando egnet for denne sårbarheten:<img width="634" height="46" alt="image" src="https://github.com/user-attachments/assets/d88a936b-9cd9-4512-a1a4-ef75649d256a" />

Scannen bekreftet at denne maskinen er sårbar for EternalBlue-sårbarheten og at det er høy risk for at det kan utføres en remote execution på denne maskinen. Dette gjør vi via Metasploit i neste steg. <img width="871" height="197" alt="image" src="https://github.com/user-attachments/assets/a4ff730f-9e33-4d46-ad00-a3889dbd9f1a" />
