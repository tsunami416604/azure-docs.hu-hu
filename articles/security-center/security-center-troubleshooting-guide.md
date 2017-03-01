---
title: "Azure Security Center – Hibaelhárítási útmutató | Microsoft Docs"
description: "Ebből a dokumentumból megismerheti az Azure Security Center használatával kapcsolatos problémák elhárításához szükséges lépéseket."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: b9f4a8b185f9fb06f8991b6da35a5d8c94689367
ms.openlocfilehash: dbbec729c14d0d9dc5781e7a88a1db3f66f7df97


---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure Security Center – Hibaelhárítási útmutató
Ez az útmutató olyan informatikai (IT) szakemberek, információbiztonsági elemzők és felhőrendszergazdák számára készült, akik szervezetei az Azure Security Centert használják, és el kell hárítaniuk a használathoz kapcsolódó problémákat.

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
Ez az útmutató a Security Center használatához kapcsolódó problémák hibaelhárítását mutatja be. A Security Center hibaelhárítási műveleteinek zöme a meghibásodott összetevőhöz tartozó [Napló](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) bejegyzéseinek áttekintésével kezdődik. A naplókból a következők állapíthatók meg:

* A végrehajtott műveletek
* A művelet kezdeményezője
* A művelet végrehajtásának időpontja
* A művelet állapota
* A művelet felderítése során hasznosítható egyéb tulajdonságok értékei

A napló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE), nem tartalmazza azonban az olvasási műveleteket (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>A figyelőügynök telepítésének hibaelhárítása Windowson
Az adatgyűjtést a Security Center figyelőügynöke végzi. Ha az adatgyűjtés engedélyezve van, és az ügynök megfelelően van telepítve a célgépen, a következő folyamatok végrehajtásának kell megkezdődnie:

* ASMAgentLauncher.exe – Azure Monitoring Agent 
* ASMMonitoringAgent.exe – Azure Security Monitoring bővítmény
* ASMSoftwareScanner.exe – Azure Scan Manager

Az Azure biztonsági figyelőbővítmény ellenőrzi a különböző biztonsági konfigurációkat, és biztonsági naplókat gyűjt a virtuális gépről. A rendszer a Scan Managert javításbeolvasóként használja.

Ha a telepítés sikeresen megtörtént, a cél virtuális gép Naplóiban az alábbihoz hasonló bejegyzés jelenik meg:

![Naplók](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

A telepítési folyamatról további információkat az ügynök naplóinak megtekintésével is kaphat. A naplók helye: *%systemdrive%\windowsazure\logs* (például: C:\WindowsAzure\Logs).

> [!NOTE]
> Ha az Azure Security Center-ügynök nem működik megfelelően, újra kell indítania a cél virtuális gépet, mivel az ügynök elindításához és leállításához nem tartozik parancs.


Ha még mindig problémákat tapasztal az adatgyűjtés során, az ügynököt az alábbi lépésekkel távolíthatja el:

1. Az **Azure Portalon** válassza ki azt a virtuális gépet, amelyen adatgyűjtési hibákat tapasztal, és kattintson a **Bővítmények** gombra.
2. Kattintson a jobb gombbal a **Microsoft.Azure.Security.Monitoring** elemre, és válassza az **Eltávolítás** parancsot.

![Ügynök eltávolítása](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig4.png)

Az Azure biztonsági figyelőbővítménynek néhány percen belül automatikusan újra kell telepítenie magát.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>A figyelőügynök telepítésének hibaelhárítása Linuxon
A virtuálisgép-ügynök telepítésének Linuxon történő hibaelhárításakor győződjön meg arról, hogy a bővítmény a következő könyvtárba lett letöltve: /var/lib/waagent/. A telepítés megtörténtének ellenőrzéséhez az alábbi parancsot futtathatja:

`cat /var/log/waagent.log` 

A hibaelhárítási céllal megtekinthető további naplófájlok: 

* /var/log/mdsd.err
* /var/log/azure/

Működő rendszer esetén kapcsolat jön létre az mdsd folyamattal a 29130-as TCP-porton. A Syslog így kommunikál az mdsd folyamattal. E viselkedés ellenőrzését az alábbi parancs futtatásával végezheti el:

`netstat -plantu | grep 29130`

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Az Endpoint Protection hibaelhárítása nem működik megfelelően

A vendégügynök a [Microsoft Antimalware](../security/azure-security-antimalware.md) bővítmény minden műveletének szülőfolyamata. Ha a vendégügynök-folyamat meghibásodik, az annak gyermekfolyamataként futó Microsoft Antimalware is meghibásodhat.  Ilyen esetekben a következők ellenőrzése javasolt:

- A cél virtuális gép egyéni rendszerkép-e, és a virtuális gép létrehozója nem telepítette-e a vendégügynököt.
- Ha a cél nem egy Windows-, hanem egy Linux-alapú virtuális gép, a kártevőírtó bővítmény Windows-verziójának telepítése egy Linux-alapú virtuális gépre sikertelen lesz. A Linux-vendégügynöknek meg kell felelnie az operációs rendszer verziójára és a szükséges csomagokra vonatkozó követelményeknek, és ha ezek a követelmények nem teljesülnek, a virtuálisgép-ügynök sem fog működni. 
- A virtuális gépet nem a vendégügynök egy régebbi verziójával hozták-e létre. Ha azzal hozták létre, vegye figyelembe, hogy néhány régebbi ügynök nem frissül automatikusan az újabb verzióra, és ez ehhez a problémához vezethet. Mindig a vendégügynök legfrissebb verzióját használja saját rendszerképek létrehozásához.
- Néhány külső felügyeleti szoftver letilthatja a vendégügynököt, vagy blokkolhatja bizonyos fájlhelyek elérését. Ha külső szoftvert telepített a virtuális gépre, ellenőrizze, hogy az ügynök szerepel-e a kizárási listán.
- Bizonyos tűzfalbeállítások vagy hálózati biztonsági csoportok (NSG) blokkolhatják a vendégügynöktől érkező, illetve az ügynök felé irányuló hálózati forgalmat.
- Bizonyos hozzáférés-vezérlési listák (ACL) megakadályozhatják a lemezhez való hozzáférést.
- Ha nincs elég hely a lemezen, az akadályozhatja a vendégügynök megfelelő működését. 

Alapértelmezés szerint a Microsoft Antimalware felhasználói felülete le van tiltva. További információkat az engedélyezéséről [a Microsoft Antimalware felhasználói felületének az Azure Resource Manager-alapú virtuális gépeken üzembe helyezés utáni engedélyezésével](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) kapcsolatos cikkben olvashat.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Az irányítópult betöltési hibáinak elhárítása

Ha problémákat tapasztal a Security Center irányítópultjának betöltése során, bizonyosodjon meg róla, hogy az előfizetést a Security Centerre regisztráló felhasználó (azaz az első felhasználó, aki megnyitotta a Security Centert az előfizetéssel) és az adatgyűjtést bekapcsolni kívánó felhasználó *Tulajdonos* vagy *Közreműködő* az előfizetésen. Ettől a pillanattól az előfizetés *Olvasó* jogú felhasználói is látják az irányítópultot/riasztásokat/ajánlásokat/házirendeket.

## <a name="contacting-microsoft-support"></a>Kapcsolatfelvétel a Microsoft támogatási szolgálatával
Bizonyos problémák a jelen cikk irányelveinek használatával azonosíthatók, a továbbiak leírása pedig a Security Center nyilvános [fórumában](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) található meg. Ha esetleg további hibaelhárításra van szükség, az alábbi képen látható módon új támogatási kérelem nyitható meg az **Azure Portalon**: 

![Microsoft támogatási szolgálat](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.




<!--HONumber=Feb17_HO3-->


