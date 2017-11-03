---
title: "Ismerkedés az Azure naplóelemzés integrációs |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse az Azure naplóelemzés integrációs szolgáltatást, és integrálhatja a napló, az Azure storage, Azure-beli Auditnaplók és az Azure Security Center riasztásokat."
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
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9d39ecd513386b75b4b640721f80991caaf9ade8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Diagnostics-naplózás és a Windows-Eseménytovábbítást Azure naplóelemzés integrációját
Az Azure napló (AzLog) lehetővé teszi az Azure-erőforrások származó nyers naplók integrálása a helyszíni biztonsági információkat és az esemény felügyeleti SIEM-rendszerek. Ez az integráció lehetővé teszi egy egységes biztonsági irányítópult minden Ez az eszköz, a helyszíni vagy felhőalapú rendelkeznie, hogy akkor is összesíteni, összefüggéseket, elemzése és az alkalmazások társított biztonsági események riasztás.
>[!NOTE]
További információ Azure napló integrált, tekintse át a [Azure napló integrációjának áttekintése](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview).

Ez a cikk segít összpontosító a Azlog szolgáltatás telepítése és a szolgáltatás integrálása az Azure Diagnostics Ismerkedés az Azure napló integráció. Az Azure napló integrációs szolgáltatás lesz képes a Windows Eseménynapló-adatok összegyűjtésére az üzembe helyezett Azure IaaS virtuális gépekről a Windows biztonsági esemény csatorna. Ez nagyon hasonlít a "Eseménytovábbítást" lehet, hogy használja a helyszínen.

>[!NOTE]
>Az Azure naplóelemzés integráció kimeneti kerüljön a SIEM képes magát a SIEM által biztosított. Tekintse meg a cikk [integrálása Azure napló integrálva a helyszíni SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) további információt.

Világossá - kell az Azure napló integrációs szolgáltatás futtatásához egy fizikai vagy virtuális számítógép a Windows Server 2008 R2 vagy újabb operációs rendszer (Windows Server 2012 R2 vagy Windows Server 2016 használata javasolt).

A fizikai számítógép futtathatná a helyszíni (vagy egy tárhelyszolgáltató helyen). Ha egy virtuális gépen futó Azure napló integrációs szolgáltatást választja, hogy a virtuális gép is lehet a helyszínen vagy a nyilvános felhő, például a Microsoft Azure.

A fizikai gép vagy az Azure napló integrációs szolgáltatást futtató virtuális gépet az Azure nyilvános felhőjében hálózati kapcsolat szükséges. A cikkben található lépéseket a konfigurációs részletekkel szolgálnak.

## <a name="prerequisites"></a>Előfeltételek
Ajánlott legalább AzLog telepítéséhez a következő elemek:
* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/free/).
* A **tárfiók** , amely használható a Windows Azure diagnosztikai naplózás (előre beállított tárfiókot használja, vagy hozzon létre egy újat – fog bemutatjuk, hogyan konfigurálhatja a tárfiók a cikk későbbi részében)
  >[!NOTE]
  A forgatókönyvtől függően egy tárfiókot nem lehet szükség. Az Azure diagnostics a forgatókönyvben egy a cikkben szereplő van szükség.
* **Két rendszer**: olyan számítógépen, amelyen az Azure napló integrációs szolgáltatás elindul, és olyan gépen, amelyet figyeli a rendszer, és a naplózási információkat küldeni a Azlog szolgáltatás számítógépe rendelkezik.
   * A gép kívánja figyelni – Ez az a virtuális gép futtatásához használt egy [Azure virtuális gépen](../virtual-machines/virtual-machines-windows-overview.md)
   * Olyan számítógépen, amelyen az Azure naplóelemzés integrációs szolgáltatás; fog futni. Ezen a számítógépen a napló információtípus később importálja a saját siem-Rendszerébe gyűjt.
    * A rendszer lehet helyszíni vagy a Microsoft Azure-ban.  
    * Egy x64 futnia kell a Windows server 2008 R2 SP1 verziója vagy magasabb és .NET 4.5.1-es verziója telepítve van. Meghatározhatja, hogy a .NET-verzió telepítését a következő című cikk [hogyan: határozza meg a .NET keretrendszer verziója települt](https://msdn.microsoft.com/library/hh925568)  
    Az Azure diagnosztikai naplózásra használható Azure tárfiókkal kell rendelkeznie. Hogyan ellenőrizheti a kapcsolatot a rendszer nyújtunk a később a jelen cikkben lévő utasítások

A virtuális gép az Azure-portálon történő létrehozásának folyamatán a gyors bemutatója tekintse meg az alábbi videó.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>Telepítési szempontok
Azure napló integrációs tesztelésekor, használhatja a rendszer, amely megfelel a minimális operációsrendszer-verziókövetelményeknek. Azonban az éles környezetben a terhelés előfordulhat, hogy felfelé skálázással vagy horizontális tervezése.

Azure napló integrációs szolgáltatást (fizikai vagy virtuális gép egy példány) több példányát is futtathatja, ha esemény kötet magas. Emellett Windows (ÜVEGVATTA) és az előfizetések száma szeretne biztosítani a példányok a storage-fiókok Azure Diagnostics alapján a kapacitás egyenleg betöltése.
>[!NOTE]
Jelenleg nincs konkrét javaslatokkal szolgál, hogy mikor horizontális felskálázás az Azure naplóelemzés integrációs gépek (azaz futó gépek az Azure naplóelemzés integrációs szolgáltatás), vagy a storage-fiókok vagy előfizetések. Skálázás döntések alapjául a teljesítmény megfigyelések minden ezeket a területeket.

Lehetősége is van a vertikális felskálázás az Azure napló integrációs szolgáltatás, a teljesítmény növelése érdekében. A következő metrikák méretezése a gépeket, amelyek futtatni az Azure naplóelemzés integrációs szolgáltatást nyújt segítséget:
* Egy 8 processzor (core) gépen Azlog integráló egyetlen példányát (~1M/hour) naponta körülbelül 24 millió esemény tud feldolgozni.

* A 4-processzor (core) gépen Azlog integráló egyetlen példányát (~62.5K/hour) naponta körülbelül 1,5 millió esemény tud feldolgozni.

## <a name="install-azure-log-integration"></a>Azure naplóelemzés integráció telepítése
Azure napló integrációs telepítéséhez le kell töltenie a [Azure naplóelemzés integrációs](https://www.microsoft.com/download/details.aspx?id=53324) telepítőfájlt. A telepítő rutin futtatását, és döntse el, ha szeretne biztosítani a telemetriai adatokat a Microsoftnak.  

![Telemetria mezőben be van jelölve a telepítési képernyőn](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> Azt javasoljuk, hogy engedélyezi-e a Microsoft telemetrikus adatok gyűjtéséért felelős ügyfélfeladatot. Ez a beállítás jelének kapcsolhatja ki telemetrikus adatok gyűjtését.
>


Az Azure napló integrációs szolgáltatás telemetriai adatokat gyűjt a számítógépről, amelyen telepítve van.  

Az összegyűjtött telemetrikus adatok van:

* Az Azure naplóelemzés integráció során bekövetkező kivételek
* A lekérdezések és a feldolgozott események száma kapcsolatos metrikákat
* Mely Azlog.exe kapcsolatos parancssori kapcsolók használt statisztikák

A telepítési folyamat az alábbi videóban is tartalmazza.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>Telepítés és ellenőrzési lépések utáni
Miután befejezte a alapbeállítások rutin, tehát a feladás egy vagy több telepítésre kész lépés és ellenőrzési lépések:
1. Nyisson meg egy emelt szintű PowerShell ablakot, és keresse meg **c:\Program Files\Microsoft Azure napló integráció**
2. Az első lépés kell venni a, az importált AzLog parancsmagok segítségével. A parancsfájl futtatásával végezhető, amely **LoadAzlogModule.ps1** (figyelje meg a ". \" a következő parancsban). Típus **.\LoadAzlogModule.ps1** nyomja le az ENTER **ENTER**.  
Meg kell jelennie hasonlót Mi az az alábbi ábra jelenik meg. </br></br>
![Telemetria mezőben be van jelölve a telepítési képernyőn](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. Most szeretne beállítani a AzLog egy adott Azure környezet használata. Az "Azure" környezete a "type" a használni kívánt Azure-felhőbe adatközpont. Miközben jelenleg több Azure környezetben, a jelenleg vonatkozó beállítások vagy **AzureCloud** vagy **AzureUSGovernment**.   A rendszergazda jogú PowerShell-környezetében, győződjön meg arról, hogy **c:\program files\Microsoft Azure napló integráció\** </br></br>
    Egyszer, a parancsot: </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud``(az Azure kereskedelmi)

      >[!NOTE]
      A parancs végrehajtása sikeres, ha Ön nem kap olyan visszajelzést.  A US Government Azure felhőbe használni kívánt, ha szeretné használni **AzureUSGovernment** (a - Name változó) az USA szövetségi felhő. Egyéb Azure felhők jelenleg nem támogatottak.  
4. Előtt a figyelheti, hogy a rendszer szüksége lesz Azure Diagnostics használja a tárfiók nevét.  Az Azure-portálon lépjen a **virtuális gépek** , és tekintse meg a virtuális gép, amelyet felügyel. Az a **tulajdonságok** területen válasszon **diagnosztikai beállítások**.  Kattintson a **ügynök** és jegyezze fel a megadott tárfióknév. Szüksége lesz a fiók nevét egy későbbi lépésben.
![Az Azure diagnosztikai beállításai](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Az Azure diagnosztikai beállításai](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      Ha a figyelés nem volt engedélyezve a virtuális gép létrehozásakor nyílik engedélyezheti a fent látható módon.
5. Most azt váltani a figyelmet az Azure naplóelemzés integrációs gépre. Igazolnia kell, hogy hozzáfér a tárolási fiók a rendszerből, amelyre telepítette Azure napló integráció. A fizikai számítógépen vagy az Azure napló integrációs szolgáltatást futtató virtuális gép hozzá kell férnie a tárfiókkal való adatbeolvasás Azure Diagnostics által naplózott, az egyes figyelt rendszerekhez konfigurált módon.  
  1. Letöltheti a Azure Tártallózó [Itt](http://storageexplorer.com/).
  2. A telepítő rutin végigfuttatása
  3. Kattintson a telepítés után **következő** hagyja a jelölőnégyzetet, és **indítsa el a Microsoft Azure Tártallózó** be van jelölve.  
  4. Jelentkezzen be az Azure-bA.
  5. Győződjön meg arról, hogy látja-e a tárfiók, az Azure Diagnostics konfigurált.  
![Tárfiókok](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. Figyelje meg, hogy a storage-fiókok mindössze néhány lehetőség áll rendelkezésre. Az egyik **táblák**. A **táblák** látnia kell egy **WADWindowsEventLogsTable**. </br></br>
   ![Storage-fiókok](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>Integrálása az Azure diagnosztikai naplózás
Ebben a lépésben konfigurál majd csatlakozni a naplófájlokat tartalmazó tárfiókot Azure napló integrációs szolgáltatást futtató számítógép.
Ez a lépés befejezéséhez fel kell néhány dolgot előre.  
* **FriendlyNameForSource:** azt egy rövid nevet alkalmazható a tárolási fiók, amelyet a virtuális gép Azure diagnosztikai adatainak tárolására beállított
* **StorageAccountName:** Azure diagnotics konfigurálásakor megadott tárfiók neve.  
* **StorageKey tulajdonságát:** Ez az a kulcsot a tárfiók a virtuális gépet az Azure diagnosztikai adatokat tároló.  

Hajtsa végre az alábbi lépéseket a biztonságitár-kulcs beszerzése:
 1. Keresse meg a [Azure-portálon](http://portal.azure.com).
 2. Az alsó görgessen az Azure-konzolon a navigációs ablaktáblán, és kattintson a **további szolgáltatások.**

 ![További szolgáltatások](./media/security-azure-log-integration-get-started/more-services.png)
 3. Adja meg **tárolási** a a **szűrő** szövegmezőben. Kattintson a **tárfiókok** (Ez akkor jelenik meg, miután megadta **tárolási**)

   ![Szűrő mezőbe](./media/security-azure-log-integration-get-started/filter.png)
 4. Storage-fiókok listája jelenik meg, kattintson duplán a naplók tárolásához rendelt fiókot.

   ![storage-fiókok listája](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. Kattintson a **hívóbetűk** a a **beállítások** szakasz.

  ![Tárelérési kulcsok](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. Másolás **key1** , és egy biztonságos helyre, amely a következő lépéshez végezheti el.

   ![két tárelérési kulcsok](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. Nyissa meg a kiszolgálót, hogy telepítette-e Azure napló integrációs, egy rendszergazda jogú parancssort (vegye figyelembe, hogy használjuk egy rendszergazda jogú parancssort itt, nem egy rendszergazda jogú PowerShell-konzolt).
 8. Navigáljon a **c:\Program Files\Microsoft Azure Naplóelemzés integráció**
 9. Futtassa a ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` parancsot. </br> Például ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` szeretné XML az esemény megjelennek az előfizetés-azonosító, ha az előfizetés-azonosító hozzáfűzése a rövid név: ``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` vagy például``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
Várjon 60 percet, majd a tárfiók kikerülnek események megtekintése. Megtekintéséhez nyissa meg **Eseménynapló > Windows-naplók > továbbított események** a Azlog integráló meg.

Címen a lépéseket, fent említett keresztül videót itt látható.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Mi történik, ha adat nem látható a továbbított események mappában?
Ha egy óra múlva adat nem látható a a **továbbított események** mappát, majd:

1. Ellenőrizze az Azure napló integrációs szolgáltatást futtató géphez, és győződjön meg arról, hogy Azure hozzáférhet. Kapcsolat tesztelése, próbálja megnyitni a [Azure-portálon](http://portal.azure.com) a böngészőből.
2. Győződjön meg arról, hogy a felhasználói fiók **Azlog** írási engedéllyel rendelkezik a mappához **users\Azlog**.
  <ol type="a">
   <li>Nyissa meg **Windows Explorer** </li>
  <li> Navigáljon a **c:\users** </li>
  <li> Kattintson a jobb gombbal **c:\users\Azlog** </li>
  <li> Kattintson a **biztonsági**  </li>
  <li> Kattintson a **NT Service\Azlog** , és ellenőrizze a fiók engedélyeit. Ha a fiók nem szerepel a ezen a lapon, vagy ha a megfelelő engedélyek jelenleg nem láthatók is jogot adni a fiók ezen a lapon.</li>
  </ol>
3.Győződjön meg arról, hogy a tárfiókot, a parancsban hozzáadott **Azlog forrás hozzáadása** szerepel, a parancs futtatásakor **Azlog forráslista**.
4. Ugrás a **Eseménynapló > Windows-naplók > alkalmazás** megjelenítéséhez, ha hiba történik az Azure naplóelemzés integráció jelentett.


Ha esetleges problémákat tapasztal a telepítés és konfigurálás során, nyisson egy [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md), jelölje be **napló integrációs** a szolgáltatást, amelynek támogatási kérelmet.

Egy másik támogatási lehetőség az [Azure napló integrációs MSDN fórumon](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Itt a közösségi támogathat egymással kérdéseket, válaszokat, tippeket és trükkök a legtöbbet hozhatja ki Azure napló integrációs módjáról. Emellett az Azure napló integrációs csoport ezen a fórumon figyeli, és amikor azt is segít.

## <a name="next-steps"></a>Következő lépések
Azure napló integrációs kapcsolatos további tudnivalókért tekintse meg a következő dokumentumokat:

* [A Microsoft Azure napló integráció az Azure-naplók](https://www.microsoft.com/download/details.aspx?id=53324) – letöltőközpontból részletes rendszerkövetelményeket, és telepítése Azure naplóelemzés integrációs utasításokat.
* [Bevezetés az Azure naplóelemzés integrációs](security-azure-log-integration-overview.md) – Ez a dokumentum bemutatja a Azure naplóelemzés integrációs, annak főbb funkcióit és annak működéséről.
* [Partner konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ebben a blogbejegyzésben bemutatja, hogyan használható Splunk, HP ArcSight és az IBM QRadar partneri megoldások Azure naplóelemzés-integráció konfigurálása. Ez az az aktuális útmutatást a SIEM-összetevők konfigurálásához. Ellenőrizze az SIEM gyártójánál, először a további részleteket.
* [Gyakori kérdések (GYIK) integrációs Azure naplóelemzés](security-azure-log-integration-faq.md) -Ez gyakran ismételt kérdések Azure naplóelemzés integrációs kapcsolatos kérdésekre ad választ.
* [A Security Center integrálása az Azure-ral riasztások jelentkezzen integrációs](../security-center/security-center-integrating-alerts-with-log-integration.md) – Ez a dokumentum azt ismerteti, hogyan a Security Center riasztásait, együtt a virtuális gép biztonsági eseményeket a naplóelemzési az Azure Diagnostics és az Azure tevékenységi naplóit, által gyűjtött szinkronizálása vagy SIEM-megoldás.
* [Az Azure diagnostics és az Azure-beli Auditnaplók újdonságai](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – ebben a blogbejegyzésben bemutatja az Azure naplói, és egyéb szolgáltatásokat, amelyek segítenek betekintést nyerhet az Azure-erőforrások működésére.
