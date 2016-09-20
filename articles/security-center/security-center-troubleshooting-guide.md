<properties
   pageTitle="Azure Security Center – Hibaelhárítási útmutató | Microsoft Azure"
   description="Ebből a dokumentumból megismerheti az Azure Security Center használatával kapcsolatos problémák elhárításához szükséges lépéseket."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="yurid"/>

# Azure Security Center – Hibaelhárítási útmutató
Ez az útmutató olyan informatikai (IT) szakemberek, információbiztonsági elemzők és felhőrendszergazdák számára készült, akik szervezetei az Azure Security Centert használják, és el kell hárítaniuk a használathoz kapcsolódó problémákat.

## Hibaelhárítási útmutató
Ez az útmutató a Security Center használatához kapcsolódó problémák hibaelhárítását mutatja be. A Security Center hibaelhárítási műveleteinek zöme a meghibásodott összetevőhöz tartozó [Napló](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) bejegyzéseinek áttekintésével kezdődik. A naplókból a következők állapíthatók meg:

- A végrehajtott műveletek
- A művelet kezdeményezője
- A művelet végrehajtásának időpontja
- A művelet állapota
- A művelet felderítése során hasznosítható egyéb tulajdonságok értékei

A napló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE), nem tartalmazza azonban az olvasási műveleteket (GET).

## A figyelőügynök telepítésének hibaelhárítása Windowson

Az adatgyűjtést a Security Center figyelőügynöke végzi. Ha az adatgyűjtés engedélyezve van, és az ügynök megfelelően van telepítve a célgépen, a következő folyamatok végrehajtásának kell megkezdődnie:

- ASMAgentLauncher.exe – Azure Monitoring Agent 
- ASMMonitoringAgent.exe – Azure Security Monitoring bővítmény
- ASMSoftwareScanner.exe – Azure Scan Manager

Az Azure biztonsági figyelőbővítmény ellenőrzi a különböző biztonsági konfigurációkat, és biztonsági naplókat gyűjt a virtuális gépről. A rendszer a Scan Managert javításbeolvasóként használja.

Ha a telepítés sikeresen megtörtént, a cél virtuális gép Naplóiban az alábbihoz hasonló bejegyzés jelenik meg:

![Naplók](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

A telepítési folyamatról további információkat az ügynök naplóinak megtekintésével is kaphat. A naplók helye: *%systemdrive%\windowsazure\logs* (például: C:\WindowsAzure\Logs).

> [AZURE.NOTE] Ha az Azure Security Center-ügynök nem működik megfelelően, újra kell indítania a cél virtuális gépet, mivel az ügynök elindításához és leállításához nem tartozik parancs.

## A figyelőügynök telepítésének hibaelhárítása Linuxon
A virtuálisgép-ügynök telepítésének Linuxon történő hibaelhárításakor győződjön meg arról, hogy a bővítmény a következő könyvtárba lett letöltve: /var/lib/waagent/. A telepítés megtörténtének ellenőrzéséhez az alábbi parancsot futtathatja:

`cat /var/log/waagent.log` 

A hibaelhárítási céllal megtekinthető további naplófájlok: 

- /var/log/mdsd.err
- /var/log/azure/

Működő rendszer esetén kapcsolat jön létre az mdsd folyamattal a 29130-as TCP-porton. A Syslog így kommunikál az mdsd folyamattal. E viselkedés ellenőrzését az alábbi parancs futtatásával végezheti el:

`netstat -plantu | grep 29130`

## Kapcsolatfelvétel a Microsoft támogatási szolgálatával

Bizonyos problémák a jelen cikk irányelveinek használatával azonosíthatók, a továbbiak leírása pedig a Security Center nyilvános [fórumában](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) található meg. Ha esetleg további hibaelhárításra van szükség, az alábbi képen látható módon új támogatási kérelem nyitható meg az Azure Portalon: 

![Microsoft támogatási szolgálat](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## Lásd még:

Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
- [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
- [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=sep16_HO1-->


