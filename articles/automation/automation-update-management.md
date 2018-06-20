---
title: Frissítse a felügyeleti megoldás az Azure-ban
description: A cikk célja annak megértéséhez, hogyan használható az Azure frissítési megoldás a Windows és Linux számítógépekre vonatkozó frissítéseket kezelheti.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8013a221f818b2aca8163e5f5195cae17c8a6a95
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232363"
---
# <a name="update-management-solution-in-azure"></a>Frissítse a felügyeleti megoldás az Azure-ban

A frissítés-kezelési megoldás az Azure Automationben segítségével az operációs rendszer frissítéseinek kezelése a Windows és Linux számítógépek, az Azure-ban, a helyszíni környezetben, vagy más szolgáltatók telepített. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

Frissítéskezelés a virtuális gépek közvetlenül az Azure Automation-fiók a engedélyezése. Megtudhatja, hogyan lehet engedélyezni a virtuális gépeket az Automation-fiók, lásd: [több virtuális gépre vonatkozó frissítéseket kezelheti](manage-update-multi.md). Frissítéskezelés a virtuális gép panelről az Azure portálon egyetlen virtuális gép is engedélyezheti. Ebben a forgatókönyvben érhető el [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) és [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuális gépek.

## <a name="solution-overview"></a>Megoldás áttekintése

Update Management által felügyelt számítógépeken a következő konfigurációk használatával értékelése és központi telepítésével:

* Microsoft-Figyelőügynök (MMA ügynököt) a Windows vagy Linux
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation hibrid runbook-feldolgozó
* A Microsoft Update vagy a Windows Server Update Services (WSUS) a Windows rendszerű számítógépeken

Az alábbi ábrán látható konceptuális ábrázolása viselkedését, és adatfolyam hogyan a megoldás értékelésére, és alkalmazza a biztonsági frissítéseket az összes kapcsolódó, Windows Server és Linux rendszerű számítógépek a munkaterületen:

![Frissítse a felügyeleti folyamatot](media/automation-update-management/update-mgmt-updateworkflow.png)

Miután a számítógép frissítési megfelelőség szempontjából vizsgálatot végez, az ügynök továbbítja a tömeges az Azure Naplóelemzés szolgáltatáshoz. A Windows-számítógépen a megfelelőségi vizsgálat alapértelmezett 12 óránként végzi. 

A vizsgálati ütemezés szerint mellett vizsgálata frissítési megfelelőség szempontjából a frissítés telepítése előtt, ha újraindul a MMA 15 percen belül és frissítés telepítése után indítható. 

A Linux rendszerű számítógépek esetén a megfelelőségi vizsgálat alapértelmezett 3 óránként végzi. Az MMA ügynök újraindítása, ha a megfelelőségi vizsgálat 15 percen belül megkezdődik.

Jelentések hogyan naprakészen a számítógép alapul, mi a forrás úgy van konfigurálva, hogy a megoldás szinkronizálni. Ha a Windows-számítógép számára, hogy a WSUS, attól függően, hogy ha a WSUS az utolsó szinkronizálás a Microsoft Update szolgáltatással van konfigurálva az eredmények eltérőek lehetnek, a Microsoft Updates jeleníti meg. Ez megegyezik a Linux rendszerű számítógépek, amely a helyi tárház ahelyett, hogy egy nyilvános tárház a jelentést.

> [!NOTE]
> Megfelelően jelentse a szolgáltatásnak, a frissítéskezelés megköveteli a egyes URL-címek és portok engedélyezését. Ezekkel a követelményekkel kapcsolatos további tudnivalókért lásd: [hibrid feldolgozók tervezése hálózati](automation-hybrid-runbook-worker.md#network-planning).

A szoftverfrissítések központi telepítéséhez vagy telepítéséhez létrehozhat egy ütemezett üzembe helyezést a frissítést igénylő számítógépeken. Frissítés besorolása *nem kötelező* nem része a Windows rendszerű számítógépeken a központi telepítés hatóköre. Csak a szükséges frissítések szerepelnek a központi telepítés hatókörét. 

Az ütemezett telepítési határozza meg, milyen célja a számítógépek megkapják a megfelelő frissítéseket, explicit megadása a számítógépek vagy kijelölve egy [számítógépcsoport](../log-analytics/log-analytics-computer-groups.md) , amely egy meghatározott számítógépcsoportra napló átvizsgálása alapul. Is meg jóváhagyásához, és kijelölt egy adott időn belül időintervalluma frissítések telepítése ütemezés szerint. 

A telepítést az Azure Automation runbookjai végzik. Nem lehet megtekinteni, ezeknél a runbookoknál, és a runbookok nem kell konfigurálni. Amikor egy központi telepítést hoz létre, a központi telepítési ütemezés szerint elindít egy fő frissítés, a megadott időpontban a belefoglalt számítógépekhez hoz létre. A fő runbook elindítja a gyermekrunbook minden ügynök kötelező frissítések telepítésének végrehajtásához.

A megadott dátum és idő a frissítés központi telepítésben lévő, a számítógépek a központi telepítés végre párhuzamosan. A telepítés előtt a vizsgálat történik, és ellenőrizze, hogy a frissítések továbbra is szükséges. A WSUS-ügyfélszámítógépek számára ha a frissítések WSUS, a nem engedélyezett a frissítés telepítése sikertelen lesz.

## <a name="clients"></a>Ügyfelek

### <a name="supported-client-types"></a>Ügyfelek támogatott típusok

A következő táblázat a támogatott operációs rendszerek listáját jeleníti meg:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Támogatja a értékelések csak frissítése.         |
|Windows Server 2008 R2 SP1 és újabb verziók     |.NET-keretrendszer 4.5-ös vagy újabb rendszer szükséges. ([Töltse le a .NET-keretrendszer](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0-s vagy újabb rendszer szükséges. ([Töltse le a WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> A Windows PowerShell 5.1 megbízhatóbbak ajánlott.  ([Töltse le a WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. "Yum" besorolásalapú javítás szükséges CentOS nincs a beépített biztonsági adatokat.         |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14.04 LTS és 16.04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

### <a name="unsupported-client-types"></a>Ügyfél nem támogatott típusú

Az alábbi táblázat a nem támogatott operációs rendszerek:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows-ügyfél     | Ügyfél operációs rendszerek (például a Windows 7 és Windows 10) nem támogatottak.        |
|Windows Server 2016 Nano Server     | Nem támogatott.       |

### <a name="client-requirements"></a>Ügyfélkövetelmények

#### <a name="windows"></a>Windows

Windows-ügynökök be kell állítani a WSUS-kiszolgálóval való kommunikációra, vagy azokat a Microsoft Update hozzáféréssel kell rendelkeznie. Frissítéskezelés a System Center Configuration Managerrel használható. Integráció forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [integrálni System Center Configuration Manager frissítési felügyeleti](oms-solution-updatemgmt-sccmintegration.md#configuration). A [Windows-ügynök](../log-analytics/log-analytics-agent-windows.md) szükséges. Ha Ön Azure virtuális gép bevezetése a rendszer automatikusan telepíti az ügynököt.

#### <a name="linux"></a>Linux

A Linux a gép hozzáféréssel kell rendelkeznie egy frissítés tárházba. A frissítés tárház magán- vagy nyilvános lehet. Ez a megoldás nem támogatott a Linux több Naplóelemzési munkaterület jelentés konfigurált Operations Management Suite (OMS) ügynök.

Az OMS-ügynök telepítése Linux és a legújabb verzió letöltéséhez kapcsolatos információkért lásd: [Operations Management Suite-ügynök Linux](https://github.com/microsoft/oms-agent-for-linux). A Windows MOBILE ügynök telepítésével kapcsolatos információkért lásd: [Operations Management Suite Windowsra ügynök](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Engedélyek

Létrehozásához és a frissítés központi telepítések felügyeletéhez szükséges engedélyeket kell. Ezekkel a jogosultságokkal kapcsolatos további tudnivalókért lásd: [szerepköralapú hozzáférés - frissítéskezelés](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Megoldás-összetevők

A megoldás a következő erőforrások áll. Az erőforrásokat ad az Automation-fiók. Vagy közvetlenül csatlakoztatott ügynökök fontosságúak vagy egy Operations Manager csatlakoztatott felügyeleti csoportban.

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok

Miután engedélyezte a megoldás, bármely Windows-számítógép, amely közvetlenül kapcsolódik a Naplóelemzési munkaterület automatikusan konfigurálják, mint a hibrid forgatókönyv-feldolgozót a runbookok, amelyek szerepelnek a megoldás támogatásához.

Minden Windows számítógép-megoldás által kezelt, szerepel a **hibrid dolgozó csoportok** alakban egy **rendszer hibrid feldolgozócsoport** az Automation-fiókhoz. A megoldások használja az elnevezési *állomásnév FQDN_GUID*. Ezeket a csoportokat a runbookok nem tudja megcélozni a fiókban. És a sikertelen jelszómódosítás. Ezek a csoportok célja, hogy csak a-kezelési megoldás támogatja.

A Windows rendszerű számítógépeken a hibrid forgatókönyv-feldolgozó csoport Automation-forgatókönyv támogatásához, ha a megoldás és a hibrid forgatókönyv-feldolgozó csoport tagsága is ugyanazt a fiókot használja az Automation-fiók hozzáadása. Ez a funkció a hibrid forgatókönyv-feldolgozó 7.2.12024.0 verziójában lett hozzáadva.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a System Center Operations Manager felügyeleti csoport csatlakoztatva van a Naplóelemzési munkaterület, a következő felügyeleti csomagokat az Operations Manager vannak telepítve. A felügyeleti csomagok is települnek a közvetlenül csatlakoztatott Windows rendszerű számítógépeken, miután hozzáadta a megoldás. Nem kell konfigurálni, vagy a felügyeleti csomagok kezelése.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

Megoldás felügyeleti csomagok frissítésének kapcsolatos további információkért lásd: [csatlakozás az Operations Manager szolgáltatáshoz](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Győződjön meg róla, hogy az Azure-gépek előkészítve

Győződjön meg arról, hogy közvetlenül csatlakoztatott gépek kommunikáló Naplóelemzési, néhány perc múlva futtathatja egyet a következő napló keresi.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

A Windows-számítógépen tekintse át a Naplóelemzési ügynök ellenőrzésére a következő információkat:

1. A Vezérlőpult megnyitása **Microsoft Monitoring Agent**. Az a **Azure Naplóelemzés** lapon, az ügynök a következő üzenettel: **Naplóelemzési sikeresen csatlakozott a Microsoft Monitoring Agent**.
2. Nyissa meg a Windows eseménynaplóba exportálja. Ugrás a **alkalmazások és szolgáltatások Logs\Operations kezelője** , és keressen az Eseménynapló Azonosítójú 3000 és Event ID 5002 a forrás **Service Connector**. Ezek az események azt jelzi, hogy a számítógép regisztrálva van a Naplóelemzési munkaterület, és konfigurációs kap.

Ha az ügynök nem tud kommunikálni a Naplóelemzési, és az ügynök egy tűzfal vagy proxykiszolgálón keresztül az internettel történő kommunikációra van konfigurálva, győződjön meg arról, hogy a tűzfal vagy a proxy server megfelelően van-e konfigurálva. Megtudhatja, hogyan ellenőrizheti, hogy a tűzfal vagy a proxy server megfelelően van-e konfigurálva, lásd: [hálózati konfigurációt a Windows-ügynök](../log-analytics/log-analytics-agent-windows.md) vagy [hálózati konfigurációt a Linux-ügynök](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Ha a Linux rendszerek beállítva kommunikálni a proxy vagy az OMS-átjáró, és hogy bevezetési ezt a megoldást, a frissítés a *proxy.conf* engedélyeket a omiuser csoport olvasási jogosultsággal a fájl a következő használatával parancsok:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Újonnan hozzáadott Linux-ügynökök állapota **frissített** értékelését végrehajtását követően. A folyamat akár hat órát is igénybe vehet.

Ellenőrizheti, hogy az Operations Manager felügyeleti csoport kommunikál a Naplóelemzési [Naplóelemzési ellenőrzése az Operations Manager integrációja](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat a csatlakoztatott adatforrások, ez a megoldás által támogatott:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás rendszer frissítésével kapcsolatos információkat gyűjti össze a Windows-ügynökök, és ezután kezdeményezi a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás rendszer frissítésével kapcsolatos információkat gyűjti össze az Linux-ügynököt, és ezután kezdeményezi a támogatott disztribúcióiról kötelező frissítések telepítésének. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br/>Közvetlen kapcsolat az Operations Manager-ügynök szolgáltatáshoz nincs szükség. A Naplóelemzési munkaterületet adat továbbítódik a felügyeleti csoportból. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

A vizsgálatok az egyes felügyelt Windows-számítógépek naponta kétszer történik. A Windows API hívása 15 percenként lekérdezze a meghatározásához, hogy állapota megváltozott az utolsó frissítés időpontja. Ha a állapota megváltozott, a megfelelőségi vizsgálat lehet kezdeményezni. 

A vizsgálat 3 óránként történik, az egyes kezelt Linux számítógépeken.

30 perc és az irányítópult származó felügyelt számítógépek frissített adatokat 6 óra között is igénybe vehet.

## <a name="viewing-update-assessments"></a>Nézet frissítése értékelése

Válassza ki az Automation-fiók **frissítéskezelés** megtekintéséhez a gépeknek az állapotát.

Ez a nézet a gépek hiányzó frissítések, a központi telepítést és az ütemezett frissítés központi telepítések információkat biztosít. Az a **megfelelőségi oszlop**, megtekintheti az értékelés szerint a számítógép legutóbbi. Az a **ÜGYNÖK READINESS FRISSÍTI** oszlopban látható, ha a Windows update agent állapotát. Ha probléma van, jelölje ki a hivatkozásra hibaelhárítási dokumentációja, amelyek segítségével megismerheti, hogy milyen lépéseket kell tennie a probléma elhárítása érdekében.

A gép, a frissítés vagy a központi telepítési adatokat visszaadó napló keresést futtat, válassza ki a elemet a listában. A **naplófájl-keresési** ablak nyílik meg a kijelölt lekérdezés:

![Felügyeleti alapértelmezett nézet frissítése](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Frissítések telepítése

Frissítések az összes a Linux és Windows-számítógépen a munkaterületen értékelni, után telepíthet létrehozásával szükséges frissítések egy *az üzemelő példány frissítése*. Egy központi telepítés egy vagy több számítógépet a szükséges frissítések ütemezett telepítés. Megadhatja, hogy a dátum és idő a központi telepítés és számítógép-vagy számítógépek csoportja, ahhoz, hogy a telepítés hatókörét szerepeljen. A számítógépcsoportokkal kapcsolatos további információkért tekintse meg a [Log Analytics számítógépcsoportjaival](../log-analytics/log-analytics-computer-groups.md) kapcsolatos részt.

 Számítógépcsoportok szerepel a frissítések központi telepítését, ha csoporttagság ütemezés létrehozása idején csak egyszer legyen kiértékelve. Egy csoport későbbi módosításait nem tükrözi. Ez elkerülhető, hogy törli az ütemezett frissítés központi telepítését, és hozza létre újból.

> [!NOTE]
> Alapértelmezés szerint az Azure piactérről telepített Windows virtuális gépek beállítása az automatikus frissítések kap a Windows Update szolgáltatásból. Ez a viselkedés nem változik, ha ez a megoldás fel vagy Windows virtuális gépek felvétele a munkaterület. Ha nem Ön aktívan frissítések Ez a megoldás használatával, az alapértelmezett viselkedés (az automatikus frissítések alkalmazása) vonatkozik.

Az Ubuntu a karbantartási időszakon kívül alkalmazott frissítések elkerülése érdekében konfigurálja újra a felügyelet nélküli-verziófrissítő csomagot az automatikus frissítések letiltásához. A csomag konfigurálásával kapcsolatos további információkért lásd: [az Ubuntu Server útmutató témakörében automatikus frissítések](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Az igény szerinti érhetők el az Azure piactéren Red Hat Enterprise Linux (RHEL) lemezképeket alapján létrehozott virtuális gépek regisztrált eléréséhez a [Red Hat frissítés infrastruktúra (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) helyezett az Azure-ban. Más a Linux-disztribúció frissíteni kell a terjesztési online fájl tárházból következő támogatott módszerek a terjesztési.

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza ki **hiányzó frissítések** hiányzik a gépeknek a frissítések listájának megtekintéséhez. Minden egyes frissítés szerepel a listán, és választhat ki. További információt a frissítés, az operációs rendszer és a hivatkozás igénylő számítógépek számával kapcsolatos információk jelennek meg. A **naplófájl-keresési** ablaktábla megjeleníti azokat a frissítéseket további információt.

## <a name="view-update-deployments"></a>Nézet központi telepítések

Válassza ki a **telepítések frissítése** lap választásával tudja megtekinteni a meglévő központi telepítések listáját. A tábla megnyitásához válassza a központi telepítéseket bármelyikét a **központi telepítés futtatása frissítés** ablaktáblán, hogy központi telepítéséhez.

![Frissítés telepítési eredményt áttekintése](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>Hozzon létre vagy módosítsa egy frissítés telepítését

Egy új központi telepítés létrehozásához válassza **ütemezés központi telepítésének**. A **új központi telepítést** ablaktábla megnyitása. Adja meg a következő táblázatban leírt tulajdonságokkal:

| Tulajdonság | Leírás |
| --- | --- |
|Name (Név) |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Válassza ki **Linux** vagy **Windows**.|
|Gépek frissítése |A mentett kereséseket, vagy válasszon **gép** elemet a legördülő listából válassza ki, majd válassza ki az egyes gépek. |
|Frissítési besorolások|Válassza ki a szükséges összes frissítési besorolásokat. CentOS ezt nem támogatja a kezdő verzióról.|
|Kihagyandó frissítések|Adja meg a frissítések kizárása. A Windows, adja meg a Tudásbázis következő cikkét nélkül a **KB** előtag. A Linux adja meg a csomag nevét vagy helyettesítő karaktert.  |
|Ütemezési beállítások|Válassza ki a kezdési idejét, és válassza **egyszer** vagy **ismétlődő** az ismétlődés.|| Karbantartási időszak |Állítsa be a frissítéseket percek számát. Az érték nem lehet kisebb, mint 30 perc és 6 óránál. |

## <a name="update-classifications"></a>Frissítési besorolások

A következő táblázat a frissítési besorolások felügyeleti frissítse az egyes kategóriákban definícióját.

### <a name="windows"></a>Windows

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy frissítés egy adott probléma, amely kritikus, nem biztonsági hiba kezelésére szolgál.        |
|Biztonsági frissítések     | Egy frissítés egy termékspecifikus biztonsági problémára.        |
|Kumulatív frissítések     | Az egyszerű telepítés egy csomagba gyorsjavítások összesített csoportja.        |
|Funkciócsomagok     | Termékkiadáson kívül terjesztett új termékfunkciók.        |
|Szervizcsomagok     | Egy alkalmazás által használt gyorsjavítások összesített csomagja.        |
|Definíciófrissítések     | Vírus- vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Szolgáló segédprogramok vagy funkciók teljes egy vagy több feladat.        |
|Frissítések     | Egy alkalmazás vagy a jelenleg telepített fájl frissítése.        |

### <a name="linux"></a>Linux

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Frissítéseket egy meghatározott problémára vagy egy termékspecifikus biztonsági problémára.         |
|Egyéb frissítések     | Minden más frissítéseket, amelyek nem ideiglenesek kritikus vagy biztonsági frissítések nem.        |

Linux, a frissítéskezelés képes megkülönböztetni kritikus és biztonsági frissítések megfelelőségvizsgálati adatai adatok dúsító miatt a felhőben megjelenítése során. a felhőben. Javítását, a frissítéskezelés támaszkodik besorolás adatokat a számítógépen. Más azokat a terjesztéseket, eltérően CentOS nincs ezt az információt a kezdő verzióról. Ha a biztonsági adatokat a következő parancs módon konfigurált CentOS gépek, frissítéskezelés fog tudni javítás besorolás alapján.

```bash
sudo yum -q --security check-update
```

Jelenleg nincs támogatott metódus módszer natív besorolás-adatok elérhetőségét a CentOS engedélyezéséhez. Ilyenkor előfordulhat, hogy engedélyező felhasználók a saját maguk csak legjobb támogatási megadott.

## <a name="ports"></a>Portok

A következő címekre kifejezetten a kezeléséhez szükségesek. Ezeknél a címeknél kommunikációt a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

A hibrid forgatókönyv-feldolgozó által igényelt portokat kapcsolatos további információkért lásd: [hibrid feldolgozói szerepkör portok](automation-hybrid-runbook-worker.md#hybrid-worker-role).

## <a name="search-logs"></a>Keresési naplókat

Mellett a részletek a az Azure-portál által biztosított a naplók elleni keresést végezhet. Az a **változások követése** ablaktáblán válassza előbb **Naplóelemzési**. A **naplófájl-keresési** ablaktábla megnyitása.

### <a name="sample-queries"></a>Mintalekérdezések

A következő táblázat a frissítés azt jelzi, hogy ez a megoldás által gyűjtött minta napló keres:

| Lekérdezés | Leírás |
| --- | --- |
|Frissítés</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false</br>&#124;Számítógép, a cím, a KBID, a besorolás, a PublishedDate projekt |Minden számítógép, amelyről hiányzik frissítés</br>Adja hozzá az operációs rendszer korlátozni a következők egyikét:</br>OSType! = "Linux"</br>OSType == "Linux" |
| Frissítés</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false</br>&#124;Ha számítógép == "ContosoVM1.contoso.com"</br>&#124;Számítógép, a cím, a KBID, a termék, a PublishedDate projekt |Egy adott számítógépről hiányzó frissítések (cserélje le az értéket a saját számítógépnevére)|
| Esemény</br>&#124;Ha EventLevelName == "error" és a számítógép ((frissítés &#124; where (besorolási == "Biztonsági frissítések" vagy a besorolási == "Kritikus frissítések")</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false </br>&#124;különálló számítógép)) |Olyan gépek hibaeseményei, amelyeknél kritikus vagy biztonsági szükséges frissítések hiányoznak |
| Frissítés</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false</br>&#124;különböző cím |Egyedi frissítések minden számítógépnél |
| UpdateRunProgress</br>&#124;Ha InstallationStatus == "sikertelen" </br>&#124;AggregatedValue összefoglalója count() által számítógép, cím, UpdateRunName = |Futtassa egy frissítés sikertelen rendelkező számítógépek</br>Adja hozzá az operációs rendszer korlátozni a következők egyikét:</br>OSType = "Windows"</br>OSType == "Linux" |
| Frissítés</br>&#124;Ha OSType == "Linux"</br>&#124;Ha UpdateState! = "Nem szükséges" és (besorolási == "Kritikus frissítések" vagy a besorolási == "Biztonsági frissítések")</br>&#124;AggregatedValue összefoglalója = count() számítógépenként |Linux gépeire, amelyeket a csomag frissítés érhető el, amely kritikus vagy biztonsági rést listája |
| UpdateRunProgress</br>&#124;Ha UpdateRunName == "DeploymentName"</br>&#124;AggregatedValue összefoglalója = count() számítógépenként|Az ebben a frissítésfuttatásban frissített számítógépek (cserélje le az értéket a saját frissítéstelepítésének nevére) |

## <a name="integrate-with-system-center-configuration-manager"></a>Integrálás a System Center Configuration Managerrel

Az ügyfelek, akik a számítógépek, kiszolgálók és mobileszközök kezeléséhez a System Center Configuration Managerben befektettek is erőssége és a Configuration Manager szoftverfrissítések kezelését megkönnyítő lejárat támaszkodnak. A Configuration Manager a szoftver frissítés (SUM) felügyeleti ciklust részét képezi.

A felügyeleti megoldás integrálása a System Center Configuration Managerrel kapcsolatban a [integrálni System Center Configuration Manager frissítési felügyeleti](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patch-linux-machines"></a>Javítás Linux-gépek

Az alábbi szakaszok ismertetik a lehetséges problémákat Linux javítását.

### <a name="unexpected-os-level-upgrades"></a>Váratlan operációs rendszer szintű frissítések

A Linux bizonyos változatok, például a Red Hat Enterprise Linux operációs rendszer szintű frissítések keresztül csomagok fordulhatnak elő. Vezethet frissítéskezelés fut. Ha megváltoztatja a az operációs rendszer verziószáma. Frissítés felügyeleti ugyanazokat a módszereket használja, a rendszergazda a Linux-számítógép helyi használna csomagok frissítése, mert ez a viselkedés szándékosan így.

Frissítéskezelés futtatása keresztül operációsrendszer-verzió frissítése elkerüléséhez használja a **kizárási** szolgáltatás.

Red Hat Enterprise Linux a csomag neve kizárása: redhat – kiadás-server.x86_64.

![Zárja ki a Linux-csomagok](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Kritikus biztonsági javításokat nem alkalmazott /

Ha a Linux-gépekhez központilag a frissítéseket, frissítési besorolások hajthatók végre. A szűrés frissítéseket, amelyek érvényesek a megadott feltételeknek megfelelő. A szűrő alkalmazása helyileg a számítógépen a frissítés telepítésekor.

Mivel felügyeleti frissítése Frissítés dúsító a felhőben hajtja végre, néhány frissítést előfordulhat, hogy lehet megjelölt frissítése felügyeleti biztonsági hatást, annak ellenére, hogy a helyi számítógép nem rendelkezik ezt az információt. Ennek eredményeképpen ha kritikus frissítések alkalmazása a Linux-gépek, előfordulhat, amelyek nincsenek megjelölve tartalmazónak címkéz biztonsági hatása, hogy gép és a frissítéseket nem alkalmazza a frissítéseket.

Felügyeleti frissítése azonban továbbra is előfordulhat, hogy jelenti, hogy a gép, hogy nem kompatibilis, mert a megfelelő frissítésével kapcsolatos további információkat.

Frissítési besorolás frissítések telepítése nem működik a CentOS kívül a mezőbe. A SUSE kiválasztásával *csak* más frissítések a besorolás azt eredményezheti, néhány biztonsági frissíti is, ha biztonsági frissítések telepítve kapcsolódó zypper (Csomagkezelő), vagy annak függőségeit először szükség. Ez a zypper korlátozása. Egyes esetekben, akkor előfordulhat, hogy kell futtassa újra a frissítés központi telepítését, ellenőrizze, hogy a frissítés a naplóban.

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a témakör a frissítéskezelés megoldással rendelkező problémák elhárításához nyújt segítséget.

### <a name="windows"></a>Windows

Ha tapasztal, amikor megkísérli a bevezetni a megoldás vagy egy virtuális gépet, akkor ellenőrizze a **alkalmazások és szolgáltatások Logs\Operations kezelője** események, amelyek Event ID 4502 és az esemény a helyi számítógépen Eseménynapló-üzenetek tartalmazhat **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. A következő táblázat az egyes vonatkozó hibaüzeneteket és egy lehetséges megoldás emeli ki:

| Üzenet | Ok | Megoldás |
|----------|----------|----------|
| A gép nem regisztrálható a javításkezelőhöz,<br/>A regisztráció kivétel miatt meghiúsult<br/>System.InvalidOperationException: {„Üzenet”:„A gép már<br/>regisztrálva van egy másik fiókhoz. "} | Számítógép már egy másik munkaterületet, a felügyeletéhez előkészítve. | Karbantartást végez, a régi összetevők által [a hibrid forgatókönyv-csoport törlése](automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group).|
| Nem sikerült regisztrálni a gépet a javítások, regisztrációs kivétel miatt sikertelen volt<br/>System.Net.Http.HttpRequestException: Hiba történt a kérés küldése során. ---><br/>System.Net.WebException: Az alapul szolgáló kapcsolat<br/>megszakadt: Váratlan hiba<br/>történt a fogadó oldalon. ---> System.ComponentModel.Win32Exception:<br/>Az ügyfél és a kiszolgáló nem képes kommunikálni,<br/>mert nem rendelkeznek közös algoritmussal | Proxy/átjáró/tűzfal blokkolja a kommunikációt. | [Tekintse át a hálózati követelmények](automation-hybrid-runbook-worker.md#network-planning).|
| A gép nem regisztrálható a javításkezelőhöz,<br/>A regisztráció kivétel miatt meghiúsult<br/>Newtonsoft.Json.JsonReaderException: Hiba történt a pozitív végtelen érték elemzése közben. | Proxy/átjáró/tűzfal blokkolja a kommunikációt. | [Tekintse át a hálózati követelmények](automation-hybrid-runbook-worker.md#network-planning).|
| A szolgáltatás által bemutatott tanúsítványt \<wsid\>. oms.opinsights.azure.com<br/>nem a Microsoft szolgáltatásaihoz használt<br/>hitelesítésszolgáltató bocsátotta ki. Kapcsolatfelvétel<br/>a hálózati rendszergazdánál, hogy futtat-e olyan proxyt, amely elfogja a<br/>TLS/SSL-kommunikációt. |Proxy/átjáró/tűzfal blokkolja a kommunikációt. | [Tekintse át a hálózati követelmények](automation-hybrid-runbook-worker.md#network-planning).|
| A gép nem regisztrálható a javításkezelőhöz,<br/>A regisztráció kivétel miatt meghiúsult<br/>AgentService.HybridRegistration.<br/>PowerShell.Certificates.CertificateCreationException:<br/>Nem sikerült önaláírt tanúsítványt létrehozni. ---><br/>System.UnauthorizedAccessException: A hozzáférés megtagadva. | Önaláírt tanúsítvány létrehozása sikertelen. | Ellenőrizze, hogy a rendszerfióknak<br/>van-e olvasási hozzáférése a következő mappához:<br/>**C:\ProgramData\Microsoft\**<br/>** Crypto\RSA**|

### <a name="linux"></a>Linux

Frissítési menetek nem indítható el a Linux-számítógépen, ha másolatot készít a következő naplófájlt, és megőrizheti a hibaelhárítás céljából:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

Ha hiba fordul elő sikeresen Linux elindulása után frissítési, tekintse meg a feladat, a fertőzött gép futtató kimenetét. Előfordulhat, hogy a vonatkozó hibaüzeneteket a kutatás és művelet végrehajtása a Csomagkezelő a gép. Frissítéskezelés a lesz kifogástalan, a sikeres központi telepítést a Csomagkezelő igényel.

Bizonyos esetekben csomag frissítések zavarhatja frissítése felügyeleti akadályozza meg, hogy egy frissítés központi telepítés befejeződését. Ha megjelenik, amely, vagy zárja ki ezeket a csomagokat a jövőbeli frissítési menetek, vagy telepítse manuálisan a összekapcsolta magát.

Nem oldható fel egy javítási hiba, ha másolatot készít a következő naplófájlt, és megőrizni **előtt** hibaelhárítási célból elindítja a következő központi telepítéséhez:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>További lépések

Továbbra is az oktatóanyag áttekintésével megismerheti, hogyan kezelheti a frissítéseket a Windows virtuális gépek számára.

> [!div class="nextstepaction"]
> [Frissítések és javítások kezelheti a Windows Azure virtuális gépeken](automation-tutorial-update-management.md)

* A napló kereséssel [Naplóelemzési](../log-analytics/log-analytics-log-searches.md) frissítés részletes adatainak megtekintéséhez.
* [Hozzon létre a riasztások](../log-analytics/log-analytics-alerts.md) kritikus frissítések számítógépekről hiányzóként észlelésekor, vagy ha egy számítógép automatikus frissítések szolgáltatás le van tiltva.
