# Examenvoorbereiding Windows Server Beheer - Brandpunten

Aparte gpo voot computer en user maken.

## 1. Cloudlink Aanmaken (Snelkoppeling op bureaublad)
* **Waarom:** Automatische snelkoppeling naar de cloud-applicatie voor gebruikers.
* **Pad op de Server:** User Configuration > Preferences > Windows Settings > Shortcuts
* **Examenkneep:** Staat NIET onder Administrative Templates, maar onder Preferences!

## 2. USB-Sticks Blokkeren
* **Waarom:** Beveiliging tegen datadiefstal of virussen op computers.
* **Pad op de Server:** Computer Configuration > Policies > Administrative Templates > System > Removable Storage Access
* **Instelling:** "All Removable Storage classes: Deny all access" instellen op Enabled.
* **Examenkneep:** Moet gekoppeld worden aan de OU waar de COMPUTERS in staan.

## 3. Microsoft Store Uitschakelen
* **Waarom:** Voorkomen dat gebruikers zelf applicaties downloaden.
* **Pad op de Server:** User Configuration > Policies > Administrative Templates > Windows Components > Store
* **Instelling:** "Turn off the Store application" zetten op Enabled.


# 🚀 Examenvoorbereiding AM IT Systems & Devices (Niveau 3)
**Kandidaat:** Wieger Hijlkema  
**Examen-code:** B1-K2 (Beheert de infrastructuur)  
**Doel:** Alle brandpunten blindelings uitvoeren binnen 3 uur.

🚀 Examen-Cockpit (KitHub)
1. De "GPO-Zoekmachine" (Tabel)
Dit is je belangrijkste sectie. Als je de opdracht ziet, kijk je hier direct in.
Doel	Config-Pad (Snelweg)	Zoekterm (Filter)
Cloudlink	User > Preferences > Shortcuts	N.v.t. (Preferences)
USB-Sticks	Computer > Admin Templates > System	Removable
Wachtwoord	Computer > Windows Settings > Security Settings	N.v.t. (Handmatig)
CMD	User > Admin Templates > System	Command prompt
Registry	User > Admin Templates > System	Registry
Scherm	User > Admin Templates > Control Panel	Screen saver
Store	User > Admin Templates > Windows Components	Store
2. De "Panic-Button" (Checklist als het faalt)
Als een instelling niet werkt, loop deze drie stappen in deze volgorde af:
	1.	Forceer: Open CMD (Admin) op de client -> gpupdate /force
	2.	Resultaat: Typ gpresult /r -> Staat jouw GPO bij "Applied Group Policy Objects"? Zo nee: Link is fout.
	3.	Sessie: Log uit en log opnieuw in. (Geen discussie, dit is 90% van de gevallen de oplossing).
3. PerfMon "Snelheid-Kneep"
Zet dit in een apart blokje, want dit vergeet je onder stress:
•	Pad: PerfMon > Groene + > Kies counters.
•	De Schaal: Rechtsklik grafiek > Properties > Data
•	Bytes Total/sec: 0.0001
•	Current Bandwidth: 0.000001
•	Druk op OK. Als de lijnen nog plat zijn, check je schaal opnieuw!

---

## 🖥️ Deel 1: Windows Server & Active Directory (W2)

### Stap 1: Basis & Server Promoveren
1. Geef de server direct een Static IP (`172.16.1.10`) en verander de computernaam naar `DC05SP-DT`.
2. Installeer de rol **Active Directory Domain Services (AD DS)** via Server Manager.
3. ⚠️ **De Kneep:** Klik na installatie op het gele waarschuwingsvlaggetje bovenin en kies: *Promote this server to a domain controller*. Maak een nieuw forest aan: `solarplex.com`.
4. Installeer de **DHCP-rol** en vergeet de scope niet te **Authorizen** (activeren) in de DHCP-console, anders krijgen clients geen IP-adres.

### Stap 2: Organisational Units (OU) & Rechten
* **OU Structuur:** Maak in *Active Directory Users and Computers* drie hoofdmappen aan voor de steden: `Arnhem`, `Nijmegen` en `Doetinchem`.
* **NTFS Rechten:** Rechtermuisknop op de gedeelde map (D:-schijf) > *Properties* > *Security*. Zorg dat groepen alleen toegang hebben tot hun eigen afdelingsmap.

### Stap 3: GPO Brandpunten (Blindelings Navigeren)
Mochten er afwijkingen (X) zijn op het werkstation, herstel dit via de **Group Policy Management Editor** op de server via deze vaste routes:

* **Punt 10: De Cloudlink (Snelkoppeling op Bureaublad)**
  `User Configuration` > `Preferences` > `Windows Settings` > `Shortcuts`
  *(Let op: Staat onder Preferences, NIET onder Policies!)*
* **Punt 11: Microsoft Store Blokkeren**
  `User Configuration` > `Policies` > `Administrative Templates` > `Windows Components` > `Store`
  *(Zet "Turn off the Store application" op Enabled)*
* **Punt 9: USB-Sticks Blokkeren**
  `Computer Configuration` > `Policies` > `Administrative Templates` > `System` > `Removable Storage Access`
  *(Zet "All Removable Storage classes: Deny all access" op Enabled. Koppelen aan de Computer OU!)*
* **Punt 8: Scherm automatisch vergrendelen (10 minuten)**
  `User Configuration` > `Policies` > `Administrative Templates` > `Control Panel` > `Personalization`
  *(Zet de screensaver time-out op 600 seconden)*

---

## 📈 Deel 2: Realtime Performance Monitoring (W2)
Als de examinator vraagt om de prestaties te meten tijdens de datatransfer van `DB.zip`:
1. Open **Performance Monitor** en voeg via het groene plusje (+) de counters toe voor *Processor*, *Memory*, *PhysicalDisk* en *Network Adapter*.
2. ⚠️ **De Kneep (De Schaal):** Rechtermuisknop op de grafiek > *Properties* > *Data*. Wijzig de **Scale** handmatig:
   * `Bytes Total/sec` ➡️ Wijzig naar `0.0001`
   * `Current Bandwidth` ➡️ Wijzig naar `0.000001`
   *(Hierdoor gaan de lijnen live bewegen zodra de client het bestand uploadt!)*

---

## 🌐 Deel 3: Netwerkinfrastructuur & Subnetting (W1)

### Het /26 Subnet-Sjabloon (Stapgrootte = 64)
Bij een `/26` netwerk (Subnetmasker: `255.255.255.192`) reken je blindelings met blokken van **64**:

| Onderdeel | Subnet 1 (Inkoop) | Subnet 2 (Server/Printers) |
| :--- | :--- | :--- |
| **Netwerk Adres** | `192.168.10.0` | `192.168.10.64` |
| **Eerste Host** | `192.168.10.1` | `192.168.10.65` *(Server IP)* |
| **Laatste Host** | `192.168.10.62` | `192.168.10.126` |
| **Broadcast Adres** | `192.168.10.63` | `192.168.10.127` |

### Cisco Packet Tracer Kneepjes:
1. **Hardware vervangen:** Klik altijd eerst de virtuele rode aan/uit-schakelaar op de PC of Server **UIT** voordat je de netwerkkaart (NIC) eruit sleept, anders telt Packet Tracer je punten niet.
2. **Bekabeling:** De kabel vanaf de hoofdrouter moet *altijd* in de **Internet/WAN-poort** (de blauwe poort) van de Wireless Router.
3. **DHCP Reservering:** Vul het MAC-adres van de server en printer exact in onder *Setup > Address Reservation* in de Wireless Router zodat ze altijd hun vaste IP krijgen.
