---
title: Ismerkedés az Azure Log Integration |} A Microsoft Docs
description: Ismerje meg, hogyan telepítse az Azure Log Integration szolgáltatást, és a naplók az Azure Storage, Azure-auditnaplók és az Azure Security Center riasztásainak integrálása.
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
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: bf39e22c0edc23d2e452d96103c8933e0a47af9e
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304410"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Az Azure diagnosztikai naplózás és a Windows-eseménytovábbítás Azure Log Integration


>[!IMPORTANT]
> Az Azure Log integration szolgáltatás 06/01/2019 elavulttá válik. 2018. június 27. AzLog letöltések letiltottuk. Mi a teendő mozgatása előre tekintse át a hozzászólás útmutatást [SIEM-eszközök integrálása az Azure monitor](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Csak használja az Azure log integration Ha egy [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) összekötő nem érhető el a biztonsági incidens- és eseménykezelés (SIEM) gyártójától.

Az Azure Log Integration elérhetővé teszi az Azure-naplók a siem-nek, létrehozhat egy egységes biztonsági irányítópultot az összes eszköz.
Az Azure Monitor-összekötő állapotával kapcsolatos további információkért lépjen kapcsolatba a SIEM gyártójával.

> [!IMPORTANT]
> Ha elsődleges érdeklődését gyűjti a virtuális gépek naplóinak, a legtöbb SIEM-beszállítót adni ezt a lehetőséget a megoldásban. Az SIEM gyártója által biztosított összekötő használata mindig az előnyben részesített megoldás.

Ez a cikk segít az Azure Log Integration használatba. A cikk foglalkozik az Azure Log Integration szolgáltatás telepítése és a szolgáltatás integrálása az Azure Diagnostics. Az Azure Log Integration szolgáltatás majd Windows Eseménynapló-adatokat gyűjt a Windows Biztonságiesemény-csatorna az Azure infrastruktúra-szolgáltatás üzembe helyezett virtuális gépekről. Ez hasonlít *eseménytovábbítás* , amely egy a helyszíni rendszerben használható.

> [!NOTE]
> A kimenet az Azure Log Integration integrálása egy SIEM az SIEM maga végzi el. További információkért lásd: [integrálása az Azure Log Integration a helyi siem-nek](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Az Azure Log Integration szolgáltatás fut a fizikai vagy virtuális számítógép a Windows Server 2008 R2 rendszerű vagy újabb (Windows Server 2016 vagy Windows Server 2012 R2 az előnyben részesített).

Fizikai számítógép futtatható helyi vagy egy üzemeltetési helyet. Ha egy virtuális gépen az Azure Log Integration szolgáltatás futtatásához választja, a virtuális gép is lehet a helyszínen vagy a nyilvános felhőben, például a Microsoft Azure.

A fizikai vagy virtuális gép futtatása az Azure Log Integration szolgáltatás az Azure nyilvános felhő hálózati kapcsolat szükséges. Ez a cikk ismerteti a szükséges konfiguráció részleteit.

## <a name="prerequisites"></a>Előfeltételek

Minimális Azure Log Integration telepítése szükséges a következő elemek:

* Egy **Azure-előfizetés**. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* A **tárfiók** , amely a Windows Azure Diagnostics (WAD) használható naplózása. Egy előre beállított storage-fiókot, vagy hozzon létre egy új tárfiókot. Ez a cikk későbbi részében azt ismertetjük, hogyan konfigurálhatja a storage-fiókot.

  > [!NOTE]
  > A forgatókönyvtől függően egy storage-fiók nem feltétlenül szükséges. Az Azure Diagnostics a forgatókönyvben a cikkben szereplő storage-fiók megadása kötelező.

* **Két rendszer**: 
  * Az Azure Log Integration szolgáltatását futtató gép. Ezen a számítógépen a napló minden olyan információt, később a SIEM-ba importálható gyűjti. A rendszer:
    * A helyszíni vagy a Microsoft Azure-ban üzemeltetett.  
    * Egy x64 futnia kell a Windows Server 2008 R2 SP1 vagy újabb, verziója és a Microsoft .NET 4.5.1 telepítve van. Annak megállapításához, a .NET-verzió van telepítve, tekintse meg a [határozza meg, mely a .NET-keretrendszer-verziók telepítve vannak](https://msdn.microsoft.com/library/hh925568).  
    * Rendelkeznie kell kapcsolattal az Azure diagnosztikai naplózáshoz használt Azure Storage-fiókhoz. Ez a cikk későbbi részében azt ismertetjük, hogyan kapcsolat ellenőrzéséhez.
  * A gép, amelyet figyelni szeretne. Ez a futtató virtuális gép egy [Azure virtuális gép](../virtual-machines/virtual-machines-windows-overview.md). Az Azure Log Integration szolgáltatás számítógépe küld a naplózási információk erről a gépről.

A virtuális gép létrehozása az Azure portal használatával rövid bemutatójáért tekintse meg, a következő videót:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Telepítési szempontok

Tesztelés során bármilyen rendszer, amely megfelel a minimális operációsrendszer-verzió is használhatja. Az éles környezetben a terhelés szükség lehet tervezést vertikális vagy horizontális felskálázását.

Az Azure Log Integration szolgáltatás több példánya is futtatható. A szolgáltatás egy fizikai vagy virtuális gép csak egy példányát is futtathatja. Emellett terheléselosztás az Azure diagnosztikai tárfiókokhoz is WAD. A kapacitás-példányok biztosításához előfizetések száma alapján történik.

> [!NOTE]
> Jelenleg nincs konkrét javaslatokért mikor horizontális felskálázási példányok az Azure Log Integration gépek (azaz az Azure Log Integration szolgáltatást futtató gépek), vagy a storage-fiókok és -előfizetésekhez. Méretezési alapján döntéseket a teljesítmény észrevételeit a következő területeken.

A teljesítmény javítása érdekében lehetősége is van vertikális felskálázása az Azure Log Integration szolgáltatás. A következő teljesítmény-mérőszámok segítséget nyújt a méretezés, amely az Azure Log Integration szolgáltatás futtatását választja, a gépek:

* Azure-gépen 8 processzoros (mag) Azure Log Integration egyetlen példánya esemény feldolgozására képes körülbelül 24 millió / nap (körülbelül 1 millió esemény / óra).
* A 4 processzor (mag) csatlakozó Azure Log Integration egyetlen példánya esemény feldolgozására képes körülbelül 1,5 millió / nap (körülbelül óránként 62,500 események).

## <a name="install-azure-log-integration"></a>Telepítse az Azure-naplók integrációja

Haladjon végig a létrehozott rutin. Döntse el, hogy adja meg a telemetriai adatokat a Microsoftnak.

Az Azure Log Integration szolgáltatás telemetriai adatokat gyűjt a gép, amelyen telepítve van.  

Az összegyűjtött telemetriai adatokat a következőket tartalmazza:

* Az Azure Log Integration során előforduló kivételek.
* A lekérdezések és a feldolgozott események számát metrikákat.
* A statisztika kapcsolatos melyik Azlog.exe parancssori kapcsolók használhatók. 

> [!NOTE]
> Azt javasoljuk, hogy engedélyezi a Microsoftnak a telemetriai adatainak gyűjtéséről. Kikapcsolhatja a telemetriai adatok gyűjtésére törlésével a **engedélyezése a Microsoftnak telemetriai adatok gyűjtése** jelölőnégyzetet.
>

![Képernyőkép a telepítési panelről, a telemetriai adatok jelölőnégyzet be van jelölve](./media/security-azure-log-integration-get-started/telemetry.png)


A telepítési folyamat a következő videó foglalkozik:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Telepítés utáni és ellenőrzési lépések

Miután végzett az alapszintű, készen áll a telepítés utáni és ellenőrzési lépések:

1. Nyissa meg a PowerShellt rendszergazdaként. Ezután lépjen a C:\Program Files\Microsoft Azure Log Integration.
2. Az Azure Log Integration-parancsmagok importálásához. A parancsmagok importálásához futtassa a szkriptet `LoadAzlogModule.ps1`. Adjon meg `.\LoadAzlogModule.ps1`, majd nyomja le az Enter (Figyeljük meg **.\\**  ebben a parancsban). Mi jelenik meg a következő ábra például kell megjelennie:

  ![Képernyőkép a LoadAzlogModule.ps1 parancs kimenete](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Ezután konfigurálja az Azure Log Integration egy adott Azure-környezet használata. Egy *Azure-környezet* típusa, amelyet fel szeretne dolgozni az Azure felhőbeli adatközpontra. Bár vannak jelenleg több Azure-környezetek, a vonatkozó beállítások vagy **AzureCloud** vagy **AzureUSGovernment**. Futtatja a Powershellt rendszergazdaként, győződjön meg arról, hogy a C:\Program Files\Microsoft Azure Log Integration\ áll. Ezután futtassa a parancsot:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (for **AzureCloud**)
  
  Ha szeretné használni a US Government Azure-felhő, **AzureUSGovernment** a a **-név** változó. Más Azure-felhőket jelenleg nem támogatottak.  

  > [!NOTE]
  > Visszajelzés nem kapja meg, ha a parancs sikeres. 

4. A rendszer nyomon követheti, meg kell az Azure Diagnostics használt tárfiók nevére. Az Azure Portalon lépjen a **virtuális gépek**. Keresse meg a Windows virtuális gép, amely figyelni fogja. Az a **tulajdonságok** szakaszban jelölje be **diagnosztikai beállítások**.  Ezután válassza ki **ügynök**. Jegyezze fel a megadott tárfiók neve. Szüksége lesz a fiók nevét egy későbbi lépésben.

  ![Az Azure diagnosztikai beállítások panel képernyőképe](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Vendégszintű figyelés gomb engedélyezése képernyőképe](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Figyelés nem lett engedélyezve, a virtuális gép létrehozásakor, ha az előző képen látható módon engedélyezheti azt.

5. Most lépjen vissza az Azure Log Integration gép. Győződjön meg arról, hogy hozzáfér a tárfiókhoz a rendszerből, amelyre telepítette az Azure Log Integration. Az Azure Log Integration-szolgáltatást futtató számítógép kell az egyes figyelt rendszerekhez Azure Diagnostics által naplózott adatokat beolvasni a tárfiókhoz való hozzáférést. Kapcsolat meglétének ellenőrzéséhez: 
  1. [Az Azure Storage Explorer letöltése](http://storageexplorer.com/).
  2. A telepítés befejezéséhez.
  3. Ha a telepítés befejeződött, válassza ki a **tovább**. Hagyja a **indítsa el a Microsoft Azure Storage Explorer** jelölőnégyzet be van jelölve.  
  4. Jelentkezzen be az Azure-ba.
  5. Győződjön meg arról, hogy a tárfiók, beállított Azure Diagnostics látja: 

   ![Képernyőkép a storage-fiókok a Storage Explorerben](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Néhány lehetőség a storage-fiókok jelennek meg. A **táblák**, megjelenik a következő táblába **WADWindowsEventLogsTable**.

  Figyelés nem lett engedélyezve, a virtuális gép létrehozásakor, ha engedélyezheti azt, a fentebb leírt módon.


## <a name="integrate-windows-vm-logs"></a>Windows virtuális gépek naplóinak integrálása

Ebben a lépésben konfigurálja a storage-fiók, amely tartalmazza a naplófájlok csatlakozni az Azure Log Integration szolgáltatás futtató gép.

Ez a lépés utolsó mozzanataként kell néhány dolgot:  
* **FriendlyNameForSource**: Egy rövid nevet, hogy a storage-fiókot, hogy konfigurálta a virtuális gép az Azure diagnosztikai adatainak tárolására alkalmazhat.
* **StorageAccountName**: Az Azure Diagnostics konfigurálásakor megadott tárfiók neve.  
* **StorageKey tulajdonságát**: A tárkulcs a tárfiók a virtuális gép az Azure diagnosztikai adatok tárolására.  

A storage-kulcs beszerzéséhez kövesse az alábbi lépéseket:
1. Nyissa meg az [Azure Portal](http://portal.azure.com).
2. A navigációs panelen válassza ki **minden szolgáltatás**.
3. Az a **szűrő** mezőbe írja be **tárolási**. Ezután válassza ki **tárfiókok**.

  ![Képernyőkép a storage-fiókokat az összes szolgáltatás](./media/security-azure-log-integration-get-started/filter.png)

4. Storage-fiókok listája jelenik meg. Kattintson duplán a fiókot, amelyet a hozzárendelt tárolási bejelentkezni.

  ![Képernyőkép a tárfiókok listája](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet.

  ![A hozzáférési kulcs lehetőség, a menü képernyőkép](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Másolás **key1**, és mentse egy biztonságos helyen, amely a következő lépés hozzáférhet.
7. A kiszolgálón, amelyre telepítve van az Azure Log Integration nyissa meg rendszergazdaként egy parancssori ablakot. (Ügyeljen arra, nyisson meg egy parancssori ablakot rendszergazdaként, majd a PowerShell nem).
8. Nyissa meg a C:\Program Files\Microsoft Azure-naplók integrációja.
9. Futtassa a következő parancsot: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Példa:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Ha azt szeretné, hogy az előfizetés-azonosító jelenik meg az XML, Hozzáfűzés az előfizetés-azonosító rövid nevet:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Példa:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Akár 60 percig várjon, és tekintse meg az eseményeket, a rendszer a tárfiókból lekért. Az Azure Log Integration az események megtekintéséhez jelölje ki **Eseménynapló** > **Windows-naplók** > **továbbított események**.

Az alábbi videó az előző lépéseket mutatja be:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Ha az adatai nem jelennek meg a továbbított események mappában
Ha az adatai nem jelennek meg a továbbított események mappában egy óra elteltével, hajtsa végre ezeket a lépéseket:

1. Ellenőrizze a gép, amely az Azure Log Integration szolgáltatás fut-e. Győződjön meg arról, hogy hozzá tudjon férni az Azure-e. Tesztelheti a kapcsolatot, egy böngészőben, próbálja ki ugorhat a [az Azure portal](https://portal.azure.com).
2. Győződjön meg arról, hogy a felhasználói fiók Azlog rendelkezik írási engedéllyel a mappa users\Azlog.
  1. Nyissa meg a Fájlkezelőt.
  2. Ugrás a C:\users.
  3. Kattintson a jobb gombbal C:\users\Azlog.
  4. Válassza ki **biztonsági**.
  5. Válassza ki **NT Service\Azlog**. Ellenőrizze a fiók engedélyeit. Ha a fiók nem szerepel ezen a lapon, vagy ha nem jelennek meg a megfelelő engedélyekkel, megadhatja az engedélyeket a fiókhoz az ezen a lapon.
3. A parancs futtatásakor `Azlog source list`, győződjön meg arról, hogy a storage-fiók, amely a parancs hozzáadva `Azlog source add` a kimeneti szerepel-e.
4. Ha bármilyen hibát jelentett az Azure Log Integration szolgáltatásból megtekintéséhez lépjen a **Eseménynapló** > **Windows-naplók** > **alkalmazás**.

Ha problémákat tapasztal a telepítés és konfiguráció során, létrehozhat egy [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki a szolgáltatás **Naplóintegráció**.

Támogatási egy másik lehetőség a [Azure Log Integration MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Az MSDN-fórum a közösségi kérdések megválaszolását, és megosztásával, tippek és trükkök a legtöbbet hozhassa ki Azure Log Integration kapcsolatos is nyújt támogatást. Az Azure Log Integration csapata is figyeli ezt a fórumot. Minden alkalommal, amikor azok is segítenek.

## <a name="integrate-azure-activity-logs"></a>Integráció az Azure-Tevékenységnaplók

Az Azure-tevékenységnapló egy előfizetési napló, amely az Azure-ban bekövetkezett események előfizetés-szintű betekintést nyújt. Ez magában foglalja az adatok az Azure Resource Manager frissítéseket a Service Health-események operatív adatok széles. Ez a napló az Azure Security Center riasztásait is megtalálhatók.
> [!NOTE]
> Mielőtt megkísérli a jelen cikkben ismertetett lépések, nézze át a [Ismerkedés](security-azure-log-integration-get-started.md) című cikket, és hajtsa végre a lépéseket van.

### <a name="steps-to-integrate-azure-activity-logs"></a>Az Azure-Tevékenységnaplók integrálásának lépései

1. Nyissa meg a parancssort, és futtassa a következő parancsot:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Az alábbi paranccsal:  ```azlog createazureid```

    Ez a parancs az Azure bejelentkezési kéri. A parancs majd létrehoz egy Azure Active Directory egyszerű szolgáltatást az Azure AD-bérlőt, amelyek az Azure-előfizetést, amelyben a bejelentkezett felhasználó a rendszergazda, társ-rendszergazda vagy tulajdonos. A parancs sikertelen lesz, ha a bejelentkezett felhasználó csak az Azure AD-bérlő vendégfelhasználó. Hitelesítés az Azure-bA az Azure AD-n keresztül történik. Az Azure AD-identitásnak, hogy az Azure-előfizetésekből olvasási hozzáférést kap az Azure Log Integration egyszerű szolgáltatás létrehozása hoz létre.
3.  A következő paranccsal történő hitelesítéséhez az Azure Log Integration a szolgáltatásnév létrejött az előző lépés hozzáférést az előfizetéshez tartozó olvassa el a tevékenységnapló található. Meg kell lennie az előfizetés tulajdonosa a parancs futtatásához.

    ```Azlog.exe authorize subscriptionId``` Példa:

```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4.  Ellenőrizze a következő mappák győződjön meg arról, hogy az Azure Active Directory naplózási log JSON-fájlok jönnek létre számukra:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> További fejlesztéseket végzünk az adatokat a biztonsági információk és eseménykezelő (SIEM) rendszer JSON-fájljaiban szereplő információk arra vonatkozóan lépjen kapcsolatba a SIEM gyártójával.

Közösségi támogatás érhető el a [Azure Log Integration MSDN-fórumában](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Ezen a fórumon lehetővé teszi, hogy az Azure Log Integration Közösség támogatására, egymással kérdések, válaszok, tippek és trükkök a tagjai. Emellett az Azure Log Integration csapat ezen a fórumon figyeli, és minden alkalommal, amikor azt is segít.

Megnyithatja a [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki a szolgáltatás, amelynek a támogatási kért napló integrációja.

## <a name="next-steps"></a>További lépések

Azure Log Integration kapcsolatos további információkért tekintse meg a következő cikkeket: Mielőtt megkísérli a jelen cikkben ismertetett lépések, tekintse át a Get lépéseket bemutató cikkben, és hajtsa végre a lépéseket van.

* [Bevezetés az Azure Log Integration](security-azure-log-integration-overview.md). Ez a cikk bemutatja, Azure Log Integration, annak főbb funkcióit és működését.
* [Partner-konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Ebben a blogbejegyzésben bemutatja, hogyan konfigurálja az Azure Log Integration partnermegoldások Splunk, HP ArcSight és az IBM QRadar dolgozhat. Az aktuális útmutató leírja a SIEM-összetevők konfigurálásával kapcsolatos. Ellenőrizze a SIEM gyártója által biztosított további részleteket.
* [Az Azure Log Integration – gyakori kérdések (GYIK)](security-azure-log-integration-faq.md). Ez a GYIK az Azure Log Integration kapcsolatos általános kérdéseket válaszol.
* [Az Azure Security Center riasztásainak integrálása az Azure Log Integration](../security-center/security-center-integrating-alerts-with-log-integration.md). Ez a cikk bemutatja, hogyan szinkronizálhatja a Security Center riasztásait és a virtuális gép biztonsági események Azure Diagnostics és az Azure-tevékenység által gyűjtött naplók. A naplók szinkronizálja az Azure Log Analytics vagy az SIEM-megoldás használatával.
* [Új funkciók az Azure Diagnostics és az Azure-auditnaplók](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Ebben a blogbejegyzésben bemutatja az Azure-auditnaplók, és más funkciók, amelyek segítségével betekintést nyerhet az Azure-erőforrások műveletei.
