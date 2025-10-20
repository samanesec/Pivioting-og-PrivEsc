## Innholdsfortegnelse

- [Sammendrag](#sammendrag)
- [DEL A – Nmap & Metasploit](#del-a--nmap--metasploit)
- [DEL B – Packet Capture](#del-b--packet-capture)
- [DEL C – Post-eksploit og pivotering](#del-c-post-eksploit-og-pivotering)
- [DEL D – Privilegiumheving (Privilege Escalation)](#del-d-privilegiumheving-privilege-escalation)






## Sammendrag
Dette prosjektet er en sikkerhetsøvelse som simulerer vanlige angrepsvektorer innen penetrasjonstesting, pivoting og privilege escalation. Dokumentasjonen viser, steg for steg i et kontrollert labmiljø, hvordan en sårbar Windows-maskin kan utnyttes (Nmap, Metasploit, Meterpreter) for å få tilgang, samle ukryptert nettverkstrafikk (netsh → ETL → PCAP) og analysere den i Wireshark. Øvelsen demonstrerer også hvordan en angriper kan dekke spor og eskalere tilgang, og har som formål å gi opplæring i hvordan slike hendelser oppdages og håndteres — dette er ikke et reelt oppdrag, men en simulert test.

## DEL A NMAP & Metasploit
I første del av lab'en kjører vi en nmap-scan på Windows-maskinen for å se om denne er sårbar ovenfor EternalBlue-sårbarheten. Dette gjør vi ved å kjøre en nmap-kommando egnet for denne sårbarheten:<img width="634" height="46" alt="image" src="https://github.com/user-attachments/assets/d88a936b-9cd9-4512-a1a4-ef75649d256a" />

Scannen bekreftet at denne maskinen er sårbar for EternalBlue-sårbarheten og at det er høy risk for at det kan utføres en remote execution på denne maskinen. Dette gjør vi via Metasploit i neste steg. <img width="871" height="197" alt="image" src="https://github.com/user-attachments/assets/a4ff730f-9e33-4d46-ad00-a3889dbd9f1a" />

Nå som sårbarheten er bekreftet, kan du sette opp en remote execution ved hjelp av Metasploit-verktøyet. 

Vi starter opp Metasplot med «msfconsole» kommando: 

<img width="233" height="47" alt="image" src="https://github.com/user-attachments/assets/1c53b8c1-0a6e-44b8-882c-f6af88e3e441" />

På Metasploit finnes det forskjellige moduler for denne sårbarheten. For å få listet dette opp skriver vi «search ms17-010». Dette resulterte i flere valg, blant annet remote exection for EternalBlue-sårbarheten. Vi tar i bruk denne ved skrive «use 0»: <img width="940" height="118" alt="image" src="https://github.com/user-attachments/assets/771213ff-ba7d-4edd-af3a-d143115f27f4" />

Når modulen er valgt må vi sette oppgi offermaskinens IP-adresse:<img width="760" height="40" alt="image" src="https://github.com/user-attachments/assets/4585d6b8-83dc-4a03-8adc-fc86b9c9e6b8" /> <img width="754" height="40" alt="image" src="https://github.com/user-attachments/assets/b651bef4-a131-4ef6-b400-fea665f02ce7" />

Deretter setter vi Kali maskinen vår som LHOST for at offermaskinen (Windows) og Kali maskinen skal kommuniserer. Dette er viktig i en remote execution for at offermaskinen skal vite hvor den skal koble seg tilbake. Samme gjelder port, men i dette tilfelle vet vi at den allerede tar i bruk port 445. Hadde den brukt noe annet enn standardporten for SMB, ville det være nødvendig å spesifisere det.
Videre må vi sette payload og den vi velger er Meterpreter reverse shell: <img width="940" height="39" alt="image" src="https://github.com/user-attachments/assets/ab60a2e9-6505-421d-b5bc-31474c0e20d0" />

Når alt er satt kan vi starte angrepet ved å skrive «exploit»:
<img width="631" height="438" alt="image" src="https://github.com/user-attachments/assets/b15f3bd4-e6dc-4f6b-882f-15ad799c362b" />

Her får vi bekreftet at vår remote execution var vellykket. Vi kan nå gå videre til Del B av angrepet.

## DEL B Packet Capture
Første må vi starte en shell ved å skrive «shell»


<img width="538" height="146" alt="image" src="https://github.com/user-attachments/assets/319ba00a-6c14-458d-86c1-46c1f823f162" />

Nå som vi har fått tilgang skal vi gjennomføre en packet capture på nettverket. Vi skal bruke verktøyet «netsh». Dette er et legitimt Windows-verktøy som allerede er i maskinen. Her unngår vi å laste ned programmer som Wireshark, hvor installasjonen ville trigget SOC. Vi setter i gang packet capture ved å skrive følgende kommando: «netsh trace start capture=yes tracefile=c:\windows\temp\ntwrkcap.etl»
Denne kommandoen vil lagre nettverkstrafikken vi tar opp i en etl-fil på et vanlig sted som C:\windows\temp\. Her bruker vi en stealth-teknikk for å få det til å se ut som vanlig aktivitet på maskinen. 

Nå som nettverkstrafikken blir tatt opp kan vi starte på Del C av oppgaven. <img width="940" height="241" alt="image" src="https://github.com/user-attachments/assets/67652d75-a428-4ea0-90e3-35c083971654" />

## DEL C Post-eksploit og pivotering
Nå starter vi opp den sårbare VM'en og kobler oss til webserveren med Internet Explorer på Windows-maskinen:
<img width="582" height="296" alt="image" src="https://github.com/user-attachments/assets/573bfd4b-3d7d-4523-afd0-f145b539cad3" />


Vi skal nå logge inn med brukernavnet og passordet til en bruker med managerrolle. Login info ble funnet i en tidligere labøvelse


<img width="668" height="280" alt="image" src="https://github.com/user-attachments/assets/312fd228-7e87-409c-8ef7-76ca54e76a4d" />


Nå som vi har logget inn, ser vi at denne brukeren har Manager-rolle som vist i bildet. Webapplikasjonen tar i bruk HTTP protokoll, noe som gjør at nettverkstrafikken ikke er kryptert og er lesbar. Dette gjør det mulig for oss å se påloggingsinformasjonen når vi senere skal analysere nettverkstrafikken på Wireshark.

Nå skal vi tilbake til Metasploit og stoppe packet capture. Etter avlsuttet operasjon ser vi at filen er lagret som «ntwrkcap.etl» i «c:\windows\temp» filstien 


<img width="738" height="149" alt="image" src="https://github.com/user-attachments/assets/ff0e09d9-f958-4282-b24e-0b544933b371" />

## DEL D Privilegiumheving (Privilege Escalation)
For å gå tilbake til meterpreter-prompten skriver vi «exit» slik som dette: 

<img width="275" height="96" alt="image" src="https://github.com/user-attachments/assets/1162d491-15ae-4957-8986-851aee191516" />

Vi laster ned nettverkstrafikk-filen fra filbanen den ligger i og over til kali maskinen vår med følgende kommando: «download c:\\windows\\temp\\ntwrkcap.etl /home/kali».


Imotsetning til vanlig input på windows, må vi legge til en ekstra «\» på meterpreter: 

<img width="940" height="116" alt="image" src="https://github.com/user-attachments/assets/b6ab9445-2850-42d3-a4b8-89646c124518" />

For å sørge for å unngå SOC-deteksjon, sletter vi filen fra offermaskinen før vi gjør oss ferdig med dette steget. Vi gjør dette med følgende kommando:


<img width="870" height="375" alt="image" src="https://github.com/user-attachments/assets/4c3f67b8-b90f-4d32-9abf-99d11181615a" />


Her ser vi at filen ble lastet ned til /home/kali: 

<img width="595" height="240" alt="image" src="https://github.com/user-attachments/assets/dff467ce-306c-497c-9caa-3569991f8812" />


Nå må vi konvertere .etl-filen til .pcap slik at den blir lesbar med Wireshark. Dette har vi valgt å gjøre via hostmaskinen. Vi starter med å laste ned etl2pcapng fra GitHub: 

<img width="506" height="346" alt="image" src="https://github.com/user-attachments/assets/0216f306-b0f9-4872-a915-b87f37a610a6" />


Når den er lastet ned vil den lagres i «Downloads» mappen. Her flytter vi også inn .etl-filen som vi flyttet fra Kali maskinen. 


<img width="453" height="329" alt="image" src="https://github.com/user-attachments/assets/40832e5d-ee59-4a0b-9798-5e5de9bcc1c8" />


Nå som de begge ligger i samme mappe, går vi inn på Command Prompt på host maskinen for å konvertere filen. Første steg er å navigere til mappen hvor både etl2pcapng.exe og ntwrkcap.etl ligger: 

<img width="531" height="37" alt="image" src="https://github.com/user-attachments/assets/968b70fb-c388-4e73-a7d2-3302d247c4dd" />


Deretter kan vi skrive kommandoen for å konvertere filen: 

<img width="688" height="68" alt="image" src="https://github.com/user-attachments/assets/38688d79-462b-4926-9b02-c63e594cba34" />


Etter at kommandoen er kjørt ser vi at det nå er lagt til en ny fil (ntwrk.pcap) i «Downloads» mappen: 

<img width="715" height="272" alt="image" src="https://github.com/user-attachments/assets/59e01538-a5d4-46b6-8b5f-364ce1461e62" />


Nå kan vi åpne denne filen i Wireshark ved å trykke på «file» øverst på venstre side. Deretter finner vi fram til hvor .pcap-filen er lagret: 

<img width="638" height="349" alt="image" src="https://github.com/user-attachments/assets/5e2fc705-bfa9-490d-bf88-b364fb16578e" />


Nå som filen er åpnet kan vi filtrere trafikken med «http»: 

<img width="381" height="187" alt="image" src="https://github.com/user-attachments/assets/f6f770dc-16ae-4934-adcf-7a1bfe86601f" />


Når vi har fått opp all trafikken på http-protokollen kan vi se inn på følgende linje: 

<img width="761" height="317" alt="image" src="https://github.com/user-attachments/assets/02eb62bc-0c28-4e67-a6aa-a24f50bd6661" />


Her ser vi påloggingsinformasjonen vi logget inn med på andre sårbare vm'en. Dette ble tatt opp av nettverkstrafikken vi tok opp på den sårbare Windows-maskinen: 

<img width="940" height="309" alt="image" src="https://github.com/user-attachments/assets/0365a1f3-9692-419f-923e-e6c2b9399cdd" />


