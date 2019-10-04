---
title: Ismerkedés a Azure Log Integrationrel | Microsoft Docs
description: Ismerje meg, hogyan telepítheti az Azure Log Integration szolgáltatást, és hogyan integrálhatja a naplókat az Azure Storage, az Azure-naplók és a Azure Security Center riasztások segítségével.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 861a72b6abdc3d06cc4737630c92d34eee2e8545
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727689"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration Azure Diagnostics naplózással és a Windows-események továbbításával


>[!IMPORTANT]
> Az Azure log-integrációs szolgáltatást a 06/15/2019-as rendszer elavulttá teszi. A AzLog letöltése a 2018. június 27-én le lett tiltva. Útmutatás a további lépések áttekintéséhez: az [Azure monitor használata az Siem-eszközökkel való integráláshoz](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Az Azure log-integrációt csak akkor érdemes használni, ha az [Azure monitor](/azure/monitoring-and-diagnostics/monitoring-get-started) -összekötő nem érhető el a biztonsági incidens és az Event Management (SIEM) gyártójától.

Azure Log Integration elérhetővé teszi az Azure-naplókat a SIEM számára, így az összes eszközhöz létrehozhat egy egységes biztonsági irányítópultot.
Egy Azure Monitor összekötő állapotával kapcsolatos további információkért forduljon a SIEM-szállítóhoz.

> [!IMPORTANT]
> Ha az elsődleges érdek a virtuálisgép-naplók gyűjtése, akkor a legtöbb SIEM-szállító a megoldásában ezt a lehetőséget is tartalmazza. Az SIEM-szállító összekötője mindig az előnyben részesített alternatíva.

Ez a cikk segítséget nyújt a Azure Log Integration megkezdéséhez. A Azure Log Integration szolgáltatás telepítésére és a szolgáltatás Azure Diagnostics való integrálására koncentrál. A Azure Log Integration szolgáltatás ezután az Azure-infrastruktúra szolgáltatásként üzembe helyezett virtuális gépekről gyűjt Windows Eseménynapló-adatokat a Windows biztonsági esemény csatornáról. Ez hasonló a helyszíni rendszerekben esetlegesen használt *esemény* -továbbításhoz.

> [!NOTE]
> A Siem-hez tartozó Azure Log Integration kimenetének integrálását maga a SIEM végzi. További információ: az Azure Log Integration integrálása a helyszíni [Siem-](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)mel.

A Azure Log Integration szolgáltatás egy Windows Server 2008 R2 vagy újabb rendszerű fizikai vagy virtuális számítógépen fut (a Windows Server 2016 vagy a Windows Server 2012 R2 előnyben részesített).

A fizikai számítógépek helyszíni vagy üzemeltetési helyen is futtathatók. Ha úgy dönt, hogy a Azure Log Integration szolgáltatást egy virtuális gépen futtatja, akkor a virtuális gép a helyszínen vagy egy nyilvános felhőben, például a Microsoft Azure található.

A Azure Log Integration szolgáltatást futtató fizikai vagy virtuális gépnek hálózati kapcsolatra van szüksége az Azure nyilvános felhőhöz. Ez a cikk a szükséges konfigurációval kapcsolatos részletes információkat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

A Azure Log Integration telepítéséhez legalább a következő elemek szükségesek:

* Egy **Azure-előfizetés**. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* A Windows Azure Diagnostics (WAD) naplózásához használható **Storage-fiók** . Használhat előre konfigurált Storage-fiókot, vagy létrehozhat egy új Storage-fiókot. A cikk későbbi részében a Storage-fiók konfigurálását ismertetjük.

  > [!NOTE]
  > A forgatókönyvtől függően előfordulhat, hogy nincs szükség a Storage-fiókra. A cikkben ismertetett Azure Diagnostics forgatókönyv esetén szükség van egy Storage-fiókra.

* **Két rendszer**: 
  * A Azure Log Integration szolgáltatást futtató számítógép. Ez a gép összegyűjti az összes olyan naplózási információt, amelyet később importál a SIEM-be. Ez a szolgáltatás:
    * Helyszíni vagy Microsoft Azureban üzemeltethető.  
    * A Windows Server 2008 R2 SP1 vagy újabb x64-es verziójának kell futnia, és Microsoft .NET 4.5.1-es verzióját kell telepítenie. A .NET-keretrendszer telepített verziójának meghatározásához lásd: a [.NET-keretrendszer telepített verzióinak meghatározása](https://msdn.microsoft.com/library/hh925568).  
    * Kapcsolódnia kell a Azure Diagnostics naplózáshoz használt Azure Storage-fiókhoz. A cikk későbbi részében a kapcsolat megerősítését ismertetjük.
  * Egy figyelni kívánt gép. Ez egy Azure-beli [virtuális gépként](/azure/virtual-machines/virtual-machines-windows-overview)futó virtuális gép. A rendszer elküldi a számítógép naplózási információit a Azure Log Integration Service Machine szolgáltatásnak.

A virtuális gépeknek a Azure Portal használatával történő létrehozásával kapcsolatos gyors bemutatóhoz tekintse meg a következő videót:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Telepítési szempontok

A tesztelés során bármely olyan rendszert használhat, amely megfelel az operációs rendszer minimális követelményeinek. Éles környezet esetén előfordulhat, hogy a terhelésnek meg kell terveznie a horizontális felskálázást és a méretezést.

A Azure Log Integration szolgáltatás több példányát is futtathatja. Fizikai vagy virtuális gépen azonban a szolgáltatásnak csak egy példányát lehet futtatni. Emellett terheléselosztási Azure Diagnostics Storage-fiókokat is használhat a WAD-hez. A példányokhoz adni kívánt előfizetések száma a kapacitásán alapul.

> [!NOTE]
> Jelenleg nem állnak rendelkezésre konkrét javaslatok arra vonatkozóan, hogy mikor kell kibővíteni Azure Log Integration gépek (azaz a Azure Log Integration szolgáltatást futtató gépek) vagy a Storage-fiókok vagy előfizetések példányait. Az egyes területeken végzett teljesítmény-megfigyelések alapján méretezhető döntéseket hozhat.

A teljesítmény javítása érdekében lehetősége van a Azure Log Integration szolgáltatás vertikális felskálázására is. A következő teljesítménymutatók segítségével méretezhetők a Azure Log Integration szolgáltatás futtatásához kiválasztott gépek:

* A 8 processzoros (Core) gépen a Azure Log Integration egy példánya naponta 24 000 000 eseményt képes feldolgozni (körülbelül 1 000 000 esemény/óra).
* A 4 processzoros (Core) gépen a Azure Log Integration egy példánya naponta 1 500 000 eseményt képes feldolgozni (körülbelül 62 500 esemény/óra).

## <a name="install-azure-log-integration"></a>Azure Log Integration telepítése

Futtassa a set up rutint. Adja meg, hogy szeretné-e megadni a telemetria adatait a Microsoftnak.

A Azure Log Integration szolgáltatás a telemetria adatokat gyűjti arról a gépről, amelyen telepítve van.  

Az összegyűjtött telemetria adatok a következők:

* A Azure Log Integration végrehajtása során előforduló kivételek.
* A feldolgozott lekérdezések és események számával kapcsolatos mérőszámok.
* Statisztikák arról, hogy mely Azlog. exe parancssori kapcsolók vannak használatban. 

> [!NOTE]
> Javasoljuk, hogy engedélyezze a Microsoft számára a telemetria-adatok gyűjtését. A telemetria-adatok gyűjtését kikapcsolhatja a **telemetria-adatok gyűjtésének engedélyezése a Microsoft számára** jelölőnégyzet bejelölésével.
>

![Képernyőfelvétel a telepítési panelről, ha a telemetria jelölőnégyzet be van jelölve](./media/azure-log-integration-get-started/telemetry.png)


A telepítési folyamatra az alábbi videó vonatkozik:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Telepítés utáni és érvényesítési lépések

Az alapszintű telepítés befejezése után készen áll a telepítés utáni és az érvényesítési lépések elvégzésére:

1. Nyissa meg a PowerShellt rendszergazdaként. Ezután lépjen a C:\Program Files\Microsoft Azure Log Integration elemre.
2. Importálja a Azure Log Integration-parancsmagokat. A parancsmagok importálásához futtassa a parancsfájlt `LoadAzlogModule.ps1`. Írja `.\LoadAzlogModule.ps1`be a parancsot, majd nyomja le az ENTER billentyűt (jegyezze fel a használatát **.\\**  ebben a parancsban). A következő ábrán láthatóhoz hasonlóan kell megjelennie:

   ![Képernyőkép a LoadAzlogModule. ps1 parancs kimenetéről](./media/azure-log-integration-get-started/loaded-modules.png)
3. Ezután konfigurálja a Azure Log Integration egy adott Azure-környezet használatára. Az *Azure-környezet* az a fajta Azure Cloud Datacenter, amelyet használni szeretne. Bár jelenleg több Azure-környezet van, a megfelelő lehetőségek a következők: **AzureCloud** vagy **AzureUSGovernment**. Futtassa a PowerShellt rendszergazdaként, győződjön meg róla, hogy a C:\Program Files\Microsoft Azure log Integration\. található. Ezután futtassa a következő parancsot:

   `Set-AzlogAzureEnvironment -Name AzureCloud`( **AzureCloud**esetén)
  
   Ha az USA-beli kormányzati Azure-felhőt szeretné használni, használja a **AzureUSGovernment** **nevet a-name** változóhoz. A többi Azure-felhő jelenleg nem támogatott.  

   > [!NOTE]
   > A parancs sikeressége esetén nem kap visszajelzést. 

4. A rendszer figyelése előtt szüksége lesz a Azure Diagnostics használt Storage-fiók nevére. A Azure Portal lépjen a **virtuális gépek**elemre. Keresse meg azt a Windows rendszerű virtuális gépet, amelyet figyelni fog. A **Tulajdonságok** szakaszban válassza a **diagnosztikai beállítások**elemet.  Ezután válassza az **ügynök**lehetőséget. Jegyezze fel a megadott Storage-fióknév nevét. Egy későbbi lépéshez szüksége lesz erre a fiók nevére.

   ![A Azure Diagnostics beállítások panel képernyőképe](./media/azure-log-integration-get-started/storage-account-large.png) 

   ![A vendég szintű figyelés engedélyezése gomb képernyőképe](./media/azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Ha a virtuális gép létrehozásakor nem volt engedélyezve a figyelés, az előző képen látható módon engedélyezheti.

5. Most lépjen vissza a Azure Log Integration gépre. Ellenőrizze, hogy van-e kapcsolata a Storage-fiókkal abban a rendszeren, amelyre a Azure Log Integration telepítette. A Azure Log Integration szolgáltatást futtató számítógépnek hozzá kell férnie a Storage-fiókhoz, hogy lekérje az egyes figyelt rendszerek Azure Diagnostics által naplózott információkat. A kapcsolat ellenőrzése: 
   1. [Azure Storage Explorer letöltése](https://storageexplorer.com/).
   2. Fejezze be a telepítést.
   3. A telepítés befejezésekor kattintson a **Tovább gombra**. Hagyja bejelölve a **Launch Microsoft Azure Storage Explorer (indítás** ) jelölőnégyzetet.  
   4. Jelentkezzen be az Azure-ba.
   5. Ellenőrizze, hogy látható-e a Azure Diagnosticshoz konfigurált Storage-fiók: 

   ![Képernyőfelvétel a Storage Explorer lévő Storage-fiókokról](./media/azure-log-integration-get-started/storage-explorer.png)

   1. Néhány lehetőség a Storage-fiókok területen jelenik meg. A **táblák**területen egy **WADWindowsEventLogsTable**nevű táblázatot kell látnia.

   Ha a virtuális gép létrehozásakor nem engedélyezte a figyelést, akkor azt a korábban leírtak szerint engedélyezheti.


## <a name="integrate-windows-vm-logs"></a>Windows rendszerű virtuális gépek naplófájljainak integrálása

Ebben a lépésben úgy konfigurálja a Azure Log Integration szolgáltatást futtató gépet, hogy az a naplófájlokat tartalmazó Storage-fiókhoz kapcsolódjon.

A lépés elvégzéséhez néhány dolgot végre kell hajtania:  
* **FriendlyNameForSource**: Egy rövid név, amelyet a virtuális géphez konfigurált Storage-fiókra alkalmazhat, hogy az adatokat a Azure Diagnosticsról tárolja.
* **StorageAccountName**: A Azure Diagnostics konfigurálásakor megadott Storage-fiók neve.  
* **StorageKey**: Annak a Storage-fióknak a tárolási kulcsa, amelyben a Azure Diagnostics adatokat tárolják ehhez a virtuális géphez.  

A tárolási kulcs beszerzéséhez hajtsa végre a következő lépéseket:
1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. A navigációs ablaktáblán válassza a **minden szolgáltatás**lehetőséget.
3. A **szűrő** mezőbe írja be a **Storage**értéket. Ezután válassza a **Storage-fiókok**lehetőséget.

   ![Képernyőfelvétel az összes szolgáltatásban található Storage-fiókokról](./media/azure-log-integration-get-started/filter.png)

4. Megjelenik a Storage-fiókok listája. Kattintson duplán arra a fiókra, amelyet a naplózási tárolóhoz rendelt.

   ![A Storage-fiókok listáját megjelenítő képernyőkép](./media/azure-log-integration-get-started/storage-accounts.png)

5. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet.

   ![A menü hozzáférési kulcsok lehetőségét megjelenítő képernyőkép](./media/azure-log-integration-get-started/storage-account-access-keys.png)

6. Másolja a **key1**, majd mentse azt egy biztonságos helyre, amelyet a következő lépéshez érhet el.
7. A Azure Log Integration telepített kiszolgálón nyisson meg egy parancssori ablakot rendszergazdaként. (Ügyeljen arra, hogy rendszergazdaként nyisson meg egy parancssorablakot, és ne a PowerShellt.)
8. Lépjen a C:\Program Files\Microsoft Azure Log Integrationra.
9. Futtassa a következő parancsot `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`:.
 
   Példa:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Ha azt szeretné, hogy az előfizetés-azonosító megjelenjen az Event XML-ben, fűzze hozzá az előfizetés-azonosítót a felhasználóbarát névhez:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Példa:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Várjon akár 60 percet, majd tekintse meg a Storage-fiókból lekért eseményeket. Az események megtekintéséhez Azure log Integration válassza a **Eseménynapló** > **Windows-naplók** > **továbbított események**lehetőséget.

Az alábbi videó az előző lépéseket ismerteti:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Ha az adatküldés nem jelenik meg a továbbított események mappában
Ha egy óra elteltével nem jelenik meg az adatküldés a továbbított események mappában, hajtsa végre a következő lépéseket:

1. Keresse meg a Azure Log Integration szolgáltatást futtató gépet. Győződjön meg arról, hogy az elérhető az Azure-ban. A kapcsolat teszteléséhez próbáljon meg egy böngészőben megnyitni a [Azure Portal](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azlog felhasználói fiókja rendelkezik írási engedéllyel a következő mappához: users\Azlog.
   1. Nyissa meg a Fájlkezelőt.
   2. Ugrás a C:\Users.
   3. Kattintson a jobb gombbal a C:\users\Azlog.
   4. Válassza a **Biztonság**elemet.
   5. Válassza az **NT Service\Azlog**elemet. Győződjön meg a fiók engedélyeiről. Ha a fiók hiányzik ebből a lapról, vagy ha a megfelelő engedélyek nem jelennek meg, a fiók engedélyei ezen a lapon adhatók meg.
3. A parancs `Azlog source list`futtatásakor győződjön meg arról, hogy a parancsban `Azlog source add` hozzáadott Storage-fiók megjelenik a kimenetben.
4. Ha szeretné megtekinteni, hogy a rendszer a Azure log Integration szolgáltatásból származó hibákat jelentett-e, lépjen a **Eseménynapló** > **Windows-naplók** > **alkalmazáshoz**.

Ha a telepítés és a konfigurálás során problémákba ütközik, létrehozhat egy [támogatási kérést](../../azure-supportability/how-to-create-azure-support-request.md). A szolgáltatás esetében válassza a **naplózás integráció**lehetőséget.

Egy másik támogatási lehetőség a [Azure log INTEGRATION MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Az MSDN fórumában a Közösség támogatást kaphat a kérdések megválaszolásával, és megoszthatja a tippeket és trükköket, hogy miként hozhatja ki a legtöbbet Azure Log Integration. A Azure Log Integration csapat ezen a fórumon is figyeli. Minden alkalommal segítenek.

## <a name="integrate-azure-activity-logs"></a>Azure-Tevékenységnaplók integrálása

Az Azure-tevékenységnapló egy előfizetési napló, amely az Azure-ban bekövetkezett események előfizetés-szintű betekintést nyújt. Ez számos adattartományt tartalmaz, Azure Resource Manager operatív adatokból Service Health események frissítéseire. Ez a napló a Azure Security Center riasztásokat is tartalmazza.
> [!NOTE]
> A cikkben szereplő lépések megkezdése előtt tekintse át az [első](azure-log-integration-get-started.md) lépések című cikket, és végezze el a szükséges lépéseket.

### <a name="steps-to-integrate-azure-activity-logs"></a>Az Azure-Tevékenységnaplók integrálásának lépései

1. Nyissa meg a parancssort, és futtassa a következő parancsot:```cd c:\Program Files\Microsoft Azure Log Integration```
2. Futtassa a következő parancsot:```azlog createazureid```

    Ez a parancs felszólítja az Azure-bejelentkezésre. A parancs Ezután létrehoz egy Azure Active Directory egyszerű szolgáltatást azon Azure AD-bérlők számára, amelyek olyan Azure-előfizetéseket futtatnak, amelyekben a bejelentkezett felhasználó rendszergazda, társ-rendszergazda vagy tulajdonos. A parancs sikertelen lesz, ha a bejelentkezett felhasználó csak egy vendég felhasználó az Azure AD-bérlőben. Az Azure-ba történő hitelesítés az Azure AD-n keresztül történik. Egy egyszerű szolgáltatásnév létrehozása a Azure Log Integration hoz létre egy Azure AD-identitást, amely hozzáférést kap az Azure-előfizetések olvasásához.
3. A következő parancs futtatásával engedélyezheti az előző lépésben létrehozott Azure Log Integration egyszerű szolgáltatás elérését az előfizetés tevékenységi naplójához való hozzáféréshez. A parancs futtatásához az előfizetés tulajdonosának kell lennie.

   ```Azlog.exe authorize subscriptionId```Például

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. A következő mappákban ellenőrizheti, hogy a rendszer létrehozza-e a Azure Active Directory naplófájlok JSON-fájljait:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> A JSON-fájlokban lévő információk a biztonsági és az eseménykezelő (SIEM) rendszerbe való bekapcsolásával kapcsolatos konkrét utasításokért forduljon a SIEM-szolgáltatóhoz.

A közösségi segítségnyújtás a [Azure log INTEGRATION MSDN fórumán](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)keresztül érhető el. Ez a fórum lehetővé teszi, hogy a Azure Log Integration közösség tagjai kérdéseket, válaszokat, tippeket és trükköket támogassák egymásnak. Emellett a Azure Log Integration csapata figyeli ezt a fórumot, és segít, amikor csak lehet.

Létrehozhat egy [támogatási kérést](../../azure-supportability/how-to-create-azure-support-request.md)is. Válassza ki a naplózási integráció lehetőséget arra a szolgáltatásra, amelyhez támogatást kér.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Log Integrationről, tekintse meg a következő cikkeket: A cikkben szereplő lépések megkezdése előtt tekintse át az első lépések című cikket, és végezze el a szükséges lépéseket.

* [Bevezetés a Azure log Integrationba](azure-log-integration-overview.md). Ez a cikk bemutatja, hogy Azure Log Integration, annak főbb képességei, és hogyan működik.
* [Partner konfigurálásának lépései](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Ez a blogbejegyzés bemutatja, hogyan konfigurálhatja a Azure Log Integrationt a partneri megoldások splunk, a HP ArcSight és az IBM QRadar való együttműködéshez. Ismerteti az SIEM-összetevők konfigurálásának aktuális útmutatóját. További részletekért forduljon a SIEM-szolgáltatóhoz.
* [Azure log Integration gyakori kérdések (GYIK)](azure-log-integration-faq.md). Ez a gyakori kérdések a Azure Log Integrationával kapcsolatos gyakori kérdésekre adnak választ.
* [Azure Security Center riasztások integrálása Azure log Integrationokkal](/azure/security-center/security-center-integrating-alerts-with-log-integration). Ebből a cikkből megtudhatja, hogyan szinkronizálhat Security Center riasztásokat és a virtuális gép biztonsági eseményeit, amelyeket Azure Diagnostics és az Azure-tevékenység naplófájljai gyűjtenek. A naplókat a Azure Monitor naplók vagy SIEM megoldás használatával szinkronizálhatja.
* [Új szolgáltatások a Azure Diagnostics és az Azure-naplókhoz](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Ez a blogbejegyzés bemutatja az Azure-naplókat és az egyéb olyan funkciókat, amelyek segítségével betekintést nyerhet az Azure-erőforrások műveleteibe.
