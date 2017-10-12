---
title: "Azure Security Center – Hibaelhárítási útmutató | Microsoft Docs"
description: "Ebből a dokumentumból megismerheti az Azure Security Center használatával kapcsolatos problémák elhárításához szükséges lépéseket."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: yurid
ms.openlocfilehash: 0e0a0ce5c0795cec0e47cd5f729099f4762381a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure Security Center – Hibaelhárítási útmutató
Ez az útmutató olyan informatikai (IT) szakemberek, információbiztonsági elemzők és felhőrendszergazdák számára készült, akik szervezetei az Azure Security Centert használják, és el kell hárítaniuk a használathoz kapcsolódó problémákat.

>[!NOTE] 
>2017. júniusának elejétől kezdve a Security Center a Microsoft Monitoring Agent használatával gyűjti össze és tárolja az adatokat. További információk: [Az Azure Security Center Platform migrálása](security-center-platform-migration.md). A jelen cikkben található információk a Security Center a Microsoft Monitoring Agentre való váltás után elérhető funkcióit ismertetik.
>

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
Ez az útmutató a Security Center használatához kapcsolódó problémák hibaelhárítását mutatja be. A Security Center hibaelhárítása többnyire a meghibásodott összetevőhöz tartozó [auditnapló](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) bejegyzéseinek áttekintésével kezdődik. A naplókból a következők állapíthatók meg:

* A végrehajtott műveletek
* A művelet kezdeményezője
* A művelet végrehajtásának időpontja
* A művelet állapota
* A művelet felderítése során hasznosítható egyéb tulajdonságok értékei

A napló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE), nem tartalmazza azonban az olvasási műveleteket (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
A Security Center a Microsoft Monitoring Agent használatával gyűjt biztonsági adatokat az Azure-beli virtuális gépekről. Ez ugyanaz az ügynök, amelyet az Operations Management Suite és a Log Analytics szolgáltatás is használ. Ha az adatgyűjtés engedélyezve van, és az ügynök megfelelően van telepítve a célgépen, elkezdődik az alábbi folyamat végrehajtása:

* HealthService.exe

Ha megnyitja a szolgáltatáskezelő konzolt (services.msc), a Microsoft Monitoring Agent szolgáltatást is láthatja a futó szolgáltatások között az alábbi módon:

![Szolgáltatások](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Az ügynök verziójának ellenőrzéséhez nyissa meg a **Feladatkezelőt**, a **Folyamatok** lapon keresse meg a **Microsoft Monitoring Agent szolgáltatást**, kattintson rá a jobb gombbal. és kattintson a **Tulajdonságok** elemre. A **Részletek** lapon keresse meg a fájlverziót az alábbi módon:

![Fájl](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)
   

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>A Microsoft Monitoring Agent telepítési forgatókönyvei
Két telepítési forgatókönyv létezik, amelyek különböző eredményeket hozhatnak, amikor telepíti a Microsoft Monitoring Agentet a számítógépére. A támogatott forgatókönyvek:

* **A Security Center automatikusan telepítette az ügynököt**: ebben a forgatókönyvben a Security Centerben és a naplóbeli kereséssel egyaránt megtekintheti a riasztásokat. Az e-mailes értesítéseket arra az e-mail-címre kapja, amelyet az erőforrást tartalmazó előfizetés biztonsági szabályzatában adott meg.
.
* **Az ügynök manuálisan lett telepítve az Azure-beli virtuális gépre**: ebben a forgatókönyvben egy 2017 februárja előtt manuálisan letöltött és telepített ügynököt használ. Ebben az esetben csak akkor tekintheti meg a riasztásokat a Security Centerben, ha rászűr az előfizetésre, amelyhez a munkaterület tartozik. Ha arra az előfizetésre szűr, amelyhez az erőforrás tartozik, nem látja a riasztásokat. Az e-mailes értesítéseket arra az e-mail-címre kapja, amelyet a munkaterületet tartalmazó előfizetés biztonsági szabályzatában adott meg.

>[!NOTE]
> A második forgatókönyvben ismertetett viselkedés elkerülése érdekében figyeljen arra, hogy az ügynök legújabb verzióját töltse le.
> 

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>A figyelőügynök hibaelhárítása – hálózati követelmények
Ahhoz, hogy az ügynökök kapcsolódni és regisztrálni tudjanak a Security Centerben, hozzáféréssel kell rendelkezniük a hálózati erőforrásokhoz, beleértve a portszámokat és a tartományok URL-címét.

- Proxykiszolgálók esetében biztosítania kell, hogy a megfelelő proxykiszolgáló-erőforrások konfigurálva vannak az ügynök beállításaiban. További információ: [a proxybeállítások módosítása](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings).
- Ha tűzfal használatával korlátozza az internethez való hozzáférést, akkor a tűzfalat úgy kell beállítani, hogy engedélyezze az OMS hozzáférését. Az ügynök beállításait nem kell módosítania.

Az alábbi táblázat a kommunikációhoz szükséges erőforrásokat tartalmazza.

| Ügynök erőforrása | Portok | HTTPS-ellenőrzés kihagyása |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Igen |
| *.oms.opinsights.azure.com | 443 | Igen |
| *.blob.core.windows.net | 443 | Igen |
| *.azure-automation.net | 443 | Igen |

Ha problémába ütközik az ügynök előkészítése során, olvassa el a következő cikket: [Az Operations Management Suite előkészítési problémáinak hibaelhárítása](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).


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
Bizonyos problémák a jelen cikk irányelveinek használatával azonosíthatók, a továbbiak leírása pedig a Security Center nyilvános [fórumában](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) található meg. Ha további hibaelhárításra van szüksége, az alábbi képen látható módon nyithat meg új támogatási kérelmet az **Azure Portalon**: 

![Microsoft támogatási szolgálat](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

