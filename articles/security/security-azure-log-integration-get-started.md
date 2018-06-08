---
title: Ismerkedés az Azure napló integrációs |} Microsoft Docs
description: Megtudhatja, hogyan telepítse az Azure napló integrációs szolgáltatást, és integrálhatja a naplók az Azure Storage, Azure naplókat és az Azure Security Center riasztásait.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: b8888823b1445dc084ae4c0323d90110c9d384a4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839445"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Az Azure diagnosztikai naplózás és a Windows-eseménytovábbítás Azure napló-integráció

>[!IMPORTANT]
> Az Azure Naplóelemzés integrációs szolgáltatás 06/01/2019 elavulttá válik.  AzLog letöltések 2018 jún 27 letiltásra kerül. Mi a teendő áthelyezése előre tekintse át a feladás egy vagy több útmutatót [integrálható a meglévő SIEM-eszközök használata Azure-figyelő](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/preview/?cdn=disable) 

Csak akkor ajánlott Azure naplóelemzés integrációs Ha egy [Azure figyelő](../monitoring-and-diagnostics/monitoring-get-started.md) connector nem érhető el a biztonsági incidens és az esemény felügyeleti SIEM-forgalmazójától.

Azure napló-integráció elérhetővé teszi az Azure naplói a siem-rendszerébe, hogy létrehozhasson egy egységes biztonsági irányítópult minden eszköz.
Az Azure-figyelő összekötő állapotával kapcsolatos további információkért forduljon a SIEM gyártójához.

> [!IMPORTANT]
> Ha elsődleges érdeklődését van a virtuális gép naplók gyűjtésére, legtöbb SIEM-beszállítót a megoldás vegye fel ezt a beállítást. A SIEM gyártója által biztosított összekötő használata mindig az előnyben részesített megoldás.

Ez a cikk segít Ismerkedés az Azure napló integráció. Az Azure napló integrációs szolgáltatás telepítése és a szolgáltatás integrálása az Azure Diagnostics cikk foglalkozik. Az Azure napló integrációs szolgáltatás majd Windows-Eseménynapló információt gyűjt egy Azure-infrastruktúra szolgáltatásként telepített virtuális gépekről a Windows biztonsági eseményeinek csatorna. Ez hasonlít *eseménytovábbítás* , amely egy a helyszíni rendszer használhatja.

> [!NOTE]
> A kimeneti Azure napló integráció integrálása egy SIEM végezhető el a SIEM magát. További információkért lásd: [integrálni Azure napló integrálva a helyszíni SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Az Azure napló integrációs szolgáltatás fut a fizikai vagy virtuális számítógép a Windows Server 2008 R2 rendszerű vagy újabb (Windows Server 2016 vagy a Windows Server 2012 R2 használata ajánlott).

A fizikai számítógép futtatható helyi vagy üzemeltetési helyen. Ha egy virtuális gépen futó Azure napló integrációs szolgáltatást választja, a virtuális gép is lehet a helyszínen vagy a nyilvános felhőben, például a Microsoft Azure.

A fizikai gép vagy az Azure napló integrációs szolgáltatást futtató virtuális gépet az Azure nyilvános felhőjében hálózati kapcsolat szükséges. Ez a cikk ismerteti a szükséges konfigurációs részleteit.

## <a name="prerequisites"></a>Előfeltételek

Legalább a Azure napló integrációs telepítéséhez a következőkre van szükség:

* Egy **Azure-előfizetés**. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* A **tárfiók** , amely a Windows Azure diagnosztikai (ÜVEGVATTA) használható naplózása. Egy előre konfigurált tárfiókot használja, vagy hozzon létre egy új tárfiókot. A cikk későbbi részében azt ismerteti, hogyan konfigurálhatja a tárfiók.

  > [!NOTE]
  > A forgatókönyvtől függően a tárfiók nem feltétlenül szükséges. A cikkben szereplő Azure Diagnostics forgatókönyvben a storage-fiók megadása kötelező.

* **Két rendszer**: 
  * Olyan számítógépen, amelyen az Azure napló integrációs szolgáltatását futtatja. Ezen a számítógépen a napló információtípus később importálja a saját siem-Rendszerébe gyűjti. A rendszer:
    * A helyszíni vagy a Microsoft Azure-ban üzemeltetett.  
    * Egy x64 futnia kell a Windows Server 2008 R2 SP1 vagy újabb verziója és a Microsoft .NET 4.5.1-es verziója telepítve van. Annak megállapításához, a .NET-verzió van telepítve, tekintse meg a [mely .NET-keretrendszer verziója van-e telepíteni](https://msdn.microsoft.com/library/hh925568).  
    * Rendelkeznie kell az Azure Storage-fiók, amely az Azure diagnosztikai naplózás használatban van. A cikk későbbi részében azt ismerteti, hogyan való kapcsolat ellenőrzéséhez.
  * Olyan számítógépen, amelyen a figyelni kívánt. Ez a virtuális gép futtatásához használt egy [Azure virtuális gép](../virtual-machines/virtual-machines-windows-overview.md). A naplózási információk erről a gépről küld az Azure napló integrációs szolgáltatás számítógépe.

A virtuális gép létrehozása az Azure portál használatával a gyors bemutatója tekintse meg a következő videót:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Telepítési szempontok

Tesztelés során a rendszer, amely megfelel a minimális operációsrendszer-követelményeit is használhatja. Az éles környezetben a betöltés szükség lehet vertikális felskálázásával és kiterjesztése tervezése.

Az Azure napló integrációs szolgáltatás több példányát is futtathatja. A szolgáltatás egyes fizikai vagy virtuális gép csak egy példányát is futtathatja. Ezenkívül ÜVEGVATTA lehet terheléselosztásához Azure Diagnostics storage-fiókok. Az előfizetések biztosításához a példányok száma a kapacitás alapul.

> [!NOTE]
> Nem tudunk jelenleg, arról, hogy mikor horizontális Azure napló integrációs gépek (Ez azt jelenti, hogy az Azure napló integrációs szolgáltatást futtató gépek), vagy a storage-fiókok vagy előfizetések konkrét javaslatokért. Méretezési alapján döntéseket minden ezeknek a területeknek a teljesítmény megfigyelések.

A teljesítmény növelése érdekében lehetősége is van vertikális felskálázás az Azure napló integrációs szolgáltatás számára. A következő metrikák segítségére lehetnek a gépeket, amelyek az Azure napló integrációs szolgáltatás futtatásához méretezés:

* Egy 8 processzor (core) gépen Azure napló integráció egyetlen példányát (körülbelül 1 millió esemény óránként) naponta körülbelül 24 millió esemény tud feldolgozni.
* A 4-processzor (core) gépen Azure napló integráció egyetlen példányát (körülbelül óránként 62,500 események) naponta körülbelül 1,5 millió esemény tud feldolgozni.

## <a name="install-azure-log-integration"></a>Azure Naplóelemzés integráció telepítése

Azure napló integrációs telepítéséhez töltse le a [Azure napló integrációs](https://www.microsoft.com/download/details.aspx?id=53324) telepítőfájlt. A telepítés befejezéséhez. Válasszon, hogy a Microsoft telemetrikus adatok megadása.

Az Azure napló integrációs szolgáltatás telemetriai adatokat gyűjt a számítógépről, amelyen telepítve van.  

Az összegyűjtött telemetrikus adatokat az alábbiakat tartalmazza:

* Az Azure napló integráció során fellépő kivételeket.
* A lekérdezések és a feldolgozott események száma kapcsolatos metrikákat.
* A statisztika mely Azlog.exe kapcsolatos parancssori kapcsolók használhatók. 

> [!NOTE]
> Azt javasoljuk, hogy engedélyezi-e a Microsoft telemetrikus adatok gyűjtéséért felelős ügyfélfeladatot. Telemetriai adatok gyűjtésére törlésével kikapcsolható a **engedélyezése a Microsoft telemetrikus adatok gyűjtéséért felelős ügyfélfeladatot** jelölőnégyzetet.
>

![A telepítés ablaktáblán, a jelölőnégyzet bejelölésével telemetriai képernyőképe](./media/security-azure-log-integration-get-started/telemetry.png)


A telepítési folyamat a következő videó kapcsolatban lásd:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Telepítés utáni és ellenőrzési lépések

Alapszintű telepítő befejezése után készen áll a telepítés utáni és ellenőrzési lépések:

1. Nyissa meg a PowerShellt rendszergazdaként. Ezt követően folytassa a C:\Program Files\Microsoft Azure napló integráció.
2. Az Azure napló integrációs-parancsmagok importálásához. A parancsmagok importálásához futtassa a parancsfájlt `LoadAzlogModule.ps1`. Adjon meg `.\LoadAzlogModule.ps1`, majd nyomja le az Enter (vegye figyelembe a használatát **.\\**  ebben a parancsban). Mi jelenik meg az alábbi ábra hasonlót kell megjelennie:

  ![Képernyőfelvétel a LoadAzlogModule.ps1 parancs](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Ezután konfigurálja az Azure napló integrációs egy adott Azure környezet használata. Egy *Azure környezetben* használni kívánt Azure-felhőbe datacenter típusú. Bár vannak jelenleg több Azure-környezetek, a vonatkozó beállítások vagy **AzureCloud** vagy **AzureUSGovernment**. PowerShell futtatása rendszergazdaként, győződjön meg arról, hogy a C:\Program Files\Microsoft Azure napló Integration\ áll. Ezután futtassa a parancsot:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (a **AzureCloud**)
  
  Ha a US Government Azure felhőbe használni kívánt, **AzureUSGovernment** a a **-név** változó. Egyéb Azure felhők jelenleg nem támogatott.  

  > [!NOTE]
  > Visszajelzés nem érkezik, ha a parancs sikeres. 

4. Figyelheti, hogy a rendszer, meg kell, amely az Azure Diagnostics szolgál a tárfiók nevét. Az Azure-portálon lépjen **virtuális gépek**. Keresse meg a Windows rendszerű virtuális gép, amelyet felügyel. Az a **tulajdonságok** szakaszban jelölje be **diagnosztikai beállítások**.  Ezt követően válassza **ügynök**. Jegyezze meg a megadott tárfiók neve. A fiók nevét egy későbbi lépésben van szüksége.

  ![Képernyőfelvétel az Azure diagnosztikai beállítások panelen](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Képernyőkép az engedélyezés vendégszintű figyelési gomb](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Ha a figyelés nem engedélyezett, a virtuális gép létrehozásakor, a fenti ábrán látható módon engedélyezheti azt.

5. Most lépjen vissza az Azure napló integrációs gép. Ellenőrizze, hogy rendelkezik kapcsolattal a tárfiókhoz a rendszerből Azure napló integrációs telepítési. Az Azure napló integrációs szolgáltatást futtató számítógép hozzá kell férnie a tárfiókot az egyes figyelt rendszerekhez Azure Diagnostics által naplózott adatokat beolvasni. Kapcsolat ellenőrzése: 
  1. [Töltse le az Azure Storage Explorer](http://storageexplorer.com/).
  2. A telepítés befejezéséhez.
  3. Ha a telepítés befejeződött, válassza ki a **következő**. Hagyja a **indítsa el a Microsoft Azure Tártallózó** jelölőnégyzet be van jelölve.  
  4. Jelentkezzen be az Azure-ba.
  5. Győződjön meg arról, hogy látja-e a tárfiók, az Azure Diagnostics konfigurált: 

   ![Képernyőkép a Tártallózó storage-fiók](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Néhány lehetőség alatt tárfiókok jelennek meg. A **táblák**, láthatja, hogy a következő táblába **WADWindowsEventLogsTable**.

  Ha a figyelés nem engedélyezett, a virtuális gép létrehozásakor, engedélyezheti azt, a fentebb leírt módon.


## <a name="integrate-windows-vm-logs"></a>Windows virtuális gép naplók integrálása

Ebben a lépésben konfigurálja a naplófájlokat tartalmazó tárfiókot csatlakozni Azure napló integrációs szolgáltatást futtató számítógép.

E lépés elvégzése után néhány dolgot kell:  
* **FriendlyNameForSource**: egy rövid nevet, hogy a storage-fiók, amelyet a virtuális gép Azure diagnosztikai adatainak tárolására beállított alkalmazhat.
* **StorageAccountName**: az Azure Diagnostics konfigurálásakor megadott tárfiók neve.  
* **StorageKey tulajdonságát**: A kulcsot a tárfiók a virtuális gépet az Azure diagnosztikai adatokat tároló.  

A biztonságitár-kulcs beszerzése, kövesse az alábbi lépéseket:
1. Nyissa meg az [Azure Portal](http://portal.azure.com).
2. A navigációs ablaktáblán válassza ki a **minden szolgáltatás**.
3. Az a **szűrő** adja meg a **tárolási**. Ezt követően válassza **tárfiókok**.

  ![Képernyőkép a storage-fiókok a minden szolgáltatás](./media/security-azure-log-integration-get-started/filter.png)

4. A storage-fiókok listája jelenik meg. Kattintson duplán a fiókot, amelyet a hozzárendelt tárolási bejelentkezni.

  ![Képernyőkép a storage-fiókok listája](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet.

  ![A menüben a hozzáférési kulcsok beállítás képernyőkép](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Másolás **key1**, és mentse egy biztonságos helyre, amely a következő lépésben végezheti el.
7. A kiszolgálón, amelyre telepítve Azure napló integrációs nyissa meg egy parancssori ablakot rendszergazdaként. (Ügyeljen arra, hogy nyisson meg egy parancssori ablakot rendszergazdaként, majd nem PowerShell).
8. Lépjen a C:\Program Files\Microsoft Azure Naplóelemzés integráció.
9. A parancs futtatásához: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Példa:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Ha azt szeretné, hogy az előfizetés-azonosító jelenik meg az XML, az előfizetés-azonosító hozzáfűzése a rövid név:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Példa:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Várjon 60 percet, és nézze meg az eseményeket, a tárfiók kikerülnek. Azure napló integráció az események megtekintéséhez jelölje ki **Eseménynapló** > **Windows-naplók** > **továbbított események**.

Az alábbi videó az előző lépéseket tartalmazza:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Ha az adatok nem jelenik meg a továbbított események mappában
Ha az adatok nem jelenik meg a továbbított események mappában egy óra múlva, végezze az alábbi lépéseket:

1. Ellenőrizze a gép az Azure napló integrációs szolgáltatását futtató. Győződjön meg arról, hogy Azure hozzáférhet. Kapcsolatok tesztelése, a böngészőben, próbálja meg Ugrás a [Azure-portálon](http://portal.azure.com).
2. Győződjön meg arról, hogy a felhasználói fiók Azlog rendelkezik-e a mappa users\Azlog írási engedéllyel.
  1. Nyissa meg a Fájlkezelőt.
  2. Ugrás a C:\users.
  3. Kattintson a jobb gombbal C:\users\Azlog.
  4. Válassza ki **biztonsági**.
  5. Válassza ki **NT Service\Azlog**. Ellenőrizze a fiók engedélyeit. Ha a fiók nem szerepel a ezen a lapon, vagy ha nem jelennek meg a megfelelő engedélyekkel, az ezen a lapon a fiók engedélyek megadásához.
3. A parancs futtatásakor `Azlog source list`, győződjön meg arról, hogy a tárolási fiók, amely hozzá lett adva a parancs a `Azlog source add` a kimeneti szerepel.
4. Ha az Azure napló integrációs szolgáltatás ki a hibákat küld jelentést, válassza **Eseménynapló** > **Windows-naplók** > **alkalmazás**.

Ha probléma merül fel a telepítés és konfigurálás során, létrehozhat egy [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). A szolgáltatás, válassza ki a **napló integrációs**.

Egy másik támogatási lehetőség az [Azure napló integrációs MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Az MSDN fórumon az a közösségi kérdések megválaszolásával és megosztása, tippeket és használatáról a legtöbbet hozhatja ki Azure napló integrációs trükkök is támogatja. Az Azure napló integrációs csoport is ezen a fórumon figyeli. Amikor a következőkre segítenek.

## <a name="integrate-azure-activity-logs"></a>Az Azure tevékenységi naplóit integrálása

Az Azure tevékenységnapló egy előfizetési napló, amely történt az Azure-előfizetés szintű események betekintést nyújt. Ez magában foglalja az Azure Resource Manager működési adatokat a frissítésekre a szolgáltatás állapotával kapcsolatos események adatait számos. Ez a napló az Azure Security Center riasztásait is megtalálhatók.
> [!NOTE]
> A cikkben ismertetett-megkezdése előtt át kell néznie a [Ismerkedés](security-azure-log-integration-get-started.md) cikk és a lépéseket van.

### <a name="steps-to-integrate-azure-activity-logs"></a>Azure tevékenységi naplóit integrálásának lépései

1. Nyissa meg a parancssort, és futtassa a parancsot:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Futtassa ezt a parancsot:  ```azlog createazureid```

    Ez a parancs kéri az Azure bejelentkezési. A parancs majd hoz létre egy Azure Active Directory szolgáltatás egyszerű az Azure AD-bérlőkkel, amely az Azure-előfizetéseket, amelyben a bejelentkezett felhasználó nem rendszergazda, a társadminisztrátorának vagy a tulajdonos tárolni. A parancs sikertelen lesz, ha a bejelentkezett felhasználó csak a Vendég felhasználó az Azure AD-bérlő. Hitelesítés az Azure-bA az Azure AD segítségével történik. Az Azure AD identity arra, hogy az Azure-előfizetések olvasási hozzáférést egy egyszerű Azure napló integrációs szolgáltatás létrehozásakor létrejön.
3.  A következő parancsot az előző lépés a hozzáférést az olvasás a műveletnapló az előfizetés létrehozott egyszerű Azure napló integrációs szolgáltatás engedélyezése. Kell lennie az előfizetés tulajdonosa a parancs futtatásához.

    ```Azlog.exe authorize subscriptionId``` Példa:

```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```
4.  Ellenőrizze a következő mappák győződjön meg arról, hogy az Azure Active Directory JSON naplófájlok bennük jönnek létre:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> A JSON-fájlokban szereplő információk üzembe a biztonsági információk és eseménykezelő rendszer (SIEM) kapcsolatos tudnivalókat forduljon a SIEM gyártójához.

Közösségi támogatás érhető el a [Azure napló integrációs MSDN fórumon](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Ezen a fórumon lehetővé teszi, hogy a személyek Azure napló integráció közösségi egymással kérdéseket, válaszokat, tippeket és trükkök támogatásához. Ezenkívül az Azure napló integrációs csoport ezen a fórumon figyeli, és amikor azt is segít.

Is megnyithatja a [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki a napló-integráció a szolgáltatást, amelynek támogatási kérelmet.

## <a name="next-steps"></a>További lépések

Azure napló integrációs kapcsolatos további tudnivalókért tekintse meg a következő cikkeket: a cikkben ismertetett-megkezdése előtt meg kell tekintse át a Get elindult és végrehajtásához a hiba.

* [Azure napló-integráció az Azure-naplók](https://www.microsoft.com/download/details.aspx?id=53324). A letöltőközpontból részleteit, rendszerkövetelmények és telepítési utasításait Azure napló integrációs tartalmazza.
* [Bevezetés az Azure Naplóelemzés integrációs](security-azure-log-integration-overview.md). Ez a cikk bemutatja a Azure napló integrációs, annak főbb funkcióit és annak működéséről.
* [Partner konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Ebben a blogbejegyzésben bemutatja, hogyan partneri megoldások Splunk, HP ArcSight és az IBM QRadar dolgozhat Azure napló integráció konfigurálásához. Az aktuális útmutató bemutatja a SIEM-összetevők konfigurálásáról. Ellenőrizze az SIEM gyártójánál további részleteket.
* [Azure napló-integráció gyakori kérdések (GYIK)](security-azure-log-integration-faq.md). Ez a GYIK kapcsolatos kérdésekre ad közös Azure napló integráció.
* [Azure Security Center riasztásait integrálása Azure napló integrációs](../security-center/security-center-integrating-alerts-with-log-integration.md). Ez a cikk bemutatja, hogyan szinkronizálhatja a Security Center riasztásait és a virtuális gép biztonsági események Azure Diagnostics és az Azure tevékenység által gyűjtött naplók. A naplók szinkronizálni az Azure Naplóelemzés vagy SIEM megoldással.
* [Az Azure Diagnostics és az Azure új szolgáltatásai naplók](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Ebben a blogbejegyzésben bemutatja Azure naplókat, és más szolgáltatásokat tartalmaz, amely segítségével az Azure-erőforrások műveleteinek betekintést.
