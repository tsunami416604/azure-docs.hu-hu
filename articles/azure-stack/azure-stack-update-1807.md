---
title: Az Azure Stack 1807 frissítés |} A Microsoft Docs
description: További tudnivalók what's new in 1807 frissítés az Azure Stack integrált rendszerek, beleértve az ismert problémákat és hová töltse le a frissítést.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: b4ee3354d024034fd47ee50ae78d25d5bb4a38f5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345271"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a 1807 csomag tartalmát. A frissítés tartalmazza a fejlesztések, javításokat és ismert problémák az Azure Stack, valamint a helyét, a frissítés letöltése ehhez a verzióhoz. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1807 frissítés buildszáma **1.1807.0.76**.  

### <a name="new-features"></a>Új funkciók

Ez a frissítés az Azure Stack a következő fejlesztéseket tartalmazza.

<!-- 1658937 | ASDK, IS --> 
- **Indítsa el a biztonsági mentések előre meghatározott ütemezés szerint** -telepíthetőek, mint az Azure Stack is automatikusan aktiválja infrastruktúra biztonsági mentések rendszeres időközönként az emberi beavatkozás kiküszöbölése érdekében. Az Azure Stack biztonsági mentésekhez, régebbi, mint a meghatározott adatmegőrzési időszaka a külső megosztást is automatikusan törölni fogja. További információkért lásd: [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **A hozzáadott adatátviteli időt a teljes biztonsági mentés időpontja.** További információkért lásd: [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS -->  
- **Biztonsági mentési külső kapacitás most a megfelelő kapacitást a külső megosztás jeleníti meg.** (Ez korábban rögzített kód 10 GB-ra.) További információkért lásd: [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](azure-stack-backup-enable-backup-powershell.md).

<!-- 2508488 |  IS   -->
- **Bontsa ki a kapacitás** által [kiegészítő skálázási egység csomópontok hozzáadása](azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **Az Azure Resource Manager-sablonok mostantól támogatják a feltétel elem** – most már telepítheti egy erőforrást egy Azure Resource Manager-sablonban vonatkozó feltétellel. A sablon üzembe helyezéséhez egy erőforrást, egy feltételt, például kiértékelése, ha a jelenlegi paraméter értéke alapján is tervezhet. Feltételként egy sablon használatával kapcsolatos információkért lásd: [erőforrás feltételes üzembe helyezése](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) és [változók szakaszban az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) az Azure dokumentációjában olvashatók. 

   Is használhatja a sablonok [erőforrások üzembe helyezése az egynél több előfizetésen vagy erőforráscsoporton](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **A rendszer frissítette az API a Microsoft.Network erőforrás Verziótámogatás** támogatását olyan API-verzió 2017-10-01, 2015-06-15 az Azure Stack hálózati erőforrásokhoz.  2017-10-01-es és a 2015-06-15 közötti erőforrás-verziók támogatása nem érhető el ebben a kiadásban.  Tekintse meg [Azure Stack hálózati szempontjai](user/azure-stack-network-differences.md) funkcióinak különbségeit.

<!-- 2272116 | IS, ASDK   -->  
- **Az Azure Stack megjelenésével támogatottá fordított DNS-lekérdezések a kívülről elérhető az Azure Stack-infrastruktúra-végpontokra** (amely a portál, adminportal, felügyeleti és adminmanagement). Ez lehetővé teszi az Azure Stack külső végpont nevének IP-címről fogja megoldani.

<!-- 2780899 |  IS, ASDK   --> 
- **Az Azure Stack már támogatja a további hálózati adapterek hozzáadása egy meglévő virtuális Gépet.**  Ez a funkció a portal, PowerShell és CLI használatával érhető el. További információkért lásd: [hozzáadása vagy eltávolítása a hálózati adapterek](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) az Azure dokumentációjában olvashatók. 

<!-- 2222444 | IS, ASDK   -->  
- **A pontosság és a rugalmasság fejlettebbé használati mérőszámok hálózatot**.  Hálózati használati mérőszámok immár pontos és felfüggesztve fiók-előfizetésre, leállás és a versenyhelyzetek azokat.

<!-- 2753080 | IS -->  
- **Frissítés elérhető értesítések.** Azure Stack üzemelő példányok fog most rendszeres időközönként ellenőrizze egy biztonságos végpontot, és határozza meg, ha a frissítés érhető el a felhő csatlakoztatva. Ez az értesítés ugyanúgy jelennek meg, a frissítés csempén manuális ellenőrzése és a egy új frissítési importálása után. Tudjon meg többet [frissítések kezelése az Azure Stack](azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **Az Azure Stack syslog-ügyfél (előzetes verziójú funkció) fejlesztései**. Ez az ügyfél lehetővé teszi, hogy a naplózási és az Azure Stack-infrastruktúra syslog server vagy a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti szoftverek az Azure Stackhez külső naplók továbbítását. A syslog-ügyfél most már támogatja a TCP protokoll az egyszerű szöveges vagy a TLS 1.2-titkosítás, az utóbbi folyamatban van az alapértelmezett konfiguráció. Konfigurálhatja a TLS-kapcsolat csak kiszolgálói vagy kölcsönös hitelesítéssel.

  Hogyan a syslog-ügyfél kommunikál a (például a protokoll, titkosítási és hitelesítési) a syslog-kiszolgáló konfigurálásához használja a *Set-SyslogServer* parancsmagot. Ez a parancsmag a kiemelt végponthoz (EGP) érhető el.

  A syslog-ügyfél a TLS 1.2 kölcsönös hitelesítést a ügyféloldali-tanúsítvány hozzáadásához használja a Set-SyslogClient parancsmag az EGP a.

  Ebben az előzetes kiadásban megtekintheti a sokkal nagyobb számú naplók és riasztások. 

  Mivel ez a szolgáltatás még előzetes verzióban érhető el, hogy ne használjon, az éles környezetben.

  További információkért lásd: [syslog-továbbítás az Azure Stack](azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **Az Azure Resource Manager tartalmazza a régió nevét.** Ebben a kiadásban objektumokat beolvasni az Azure Resource Manager most már tartalmazza a régió neve attribútum. Ha egy meglévő PowerShell-szkriptet közvetlenül továbbítja az objektum más parancsmagok, a parancsfájl hibaüzenet és sikertelen lesz. Azure Resource Manager megfelelő működés, és megköveteli a hívó ügyfél régió attribútum kivonása céljából. További információ az Azure Resource Manager lásd [Azure Resource Manager dokumentációja](https://docs.microsoft.com/azure/azure-resource-manager/). 8 – 10 mdb--> ellenőrzése

<!-- TBD | IS, ASDK -->  
- **Delegált szolgáltatók funkció módosításait.** Az Azure-viszonteladó modell 1807 kezdve a delegált szolgáltatók modell jobban egyszerűsített annak érdekében, hogy igazodjanak, és delegált szolgáltatók nem fogja tudni a más delegált szolgáltatók, lényegében egybesimítania a modell, és így a delegált szolgáltató létrehozása a funkció elérhető a egyetlen szinten. Ahhoz, hogy a Váltás az új modell és az előfizetések kezelésével, a felhasználói előfizetéseket is most áthelyezhető új vagy meglévő Directory ugyanazt bérlőhöz tartozó delegált szolgáltatói előfizetések között. Az alapértelmezett szolgáltatója előfizetéshez tartozó felhasználói előfizetések is áthelyezhetők a Directory-bérlőben azonos delegált szolgáltatói előfizetések.  További információ: [ajánlat delegálása az Azure Stack](azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Virtuális gép létrehozáskor továbbfejlesztett** rendszerképek az Azure marketplace-ről letöltött használatával létrehozott virtuális gépek számára.

<!-- TBD | IS, ASDK -->  
- **Az Azure Stack Capacity Planner használhatóságuk**. Az Azure Stack [Capacity Planner](http://aka.ms/azstackcapacityplanner) kínál egy egyszerűsített élmény bevitelével S2D gyorsítótár és az S2D-kapacitás SKU-k megoldás meghatározásakor. Az 1000 virtuális gép korlátja el lett távolítva.


### <a name="fixed-issues"></a>Hibák kijavítva:

<!-- TBD | ASDK, IS --> 
- Különböző fejlesztések történtek a frissítési folyamat megbízhatóbbá tétele. Emellett javítások történtek-e az alapul szolgáló infrastruktúra, amely lehetséges közbeni állásidő minimálisra csökkentése a számítási feladatok a frissítés.

<!--2292271 | ASDK, IS --> 
- Ahol egy módosított kvótakorlát miatt nem vonatkozik a meglévő előfizetések probléma tudjuk rögzíteni. Most egy kvótát egy hálózati erőforráshoz tartozó ajánlat emeli, és a csomag társított felhasználói előfizetés, az új korlátozás vonatkozik a már meglévő előfizetéseket, valamint a az új előfizetési lehetőségekről.

<!-- 448955 | IS ASDK --> 
- Most már sikeresen lekérdezheti a Tevékenységnaplók az időzóna UTC + N üzembe helyezett rendszerekhez.    

<!-- 2319627 |  ASDK, IS --> 
- Biztonsági mentési konfiguráció paraméterei (elérési út/felhasználónév/jelszó/titkosítási kulcs) előzetes ellenőrzése nem megfelelő beállítások már nem a biztonsági mentési konfigurációhoz állítja be. (Korábban nem megfelelő beállítások állított be a biztonsági mentés és adatvezérelt majd sikertelen biztonsági mentés.)

<!-- 2215948 |  ASDK, IS -->
- A biztonsági mentési listában most frissíti a külső megosztást a manuálisan törölje a biztonsági mentés során.

<!-- 2332636 | IS -->  
- Frissítés erre a verzióra már nem állítja vissza az alapértelmezett szolgáltatója előfizetés alapértelmezett tulajdonosa a beépített **CloudAdmin** felhasználói és az AD FS telepítésekor.

<!-- 2360715 |  ASDK, IS -->  
- Adatközpont integrációja beállításakor, már nem elérhető az AD FS metaadatait tartalmazó fájl egy Azure-fájlmegosztást. További információkért lásd: [összevonási metaadatait tartalmazó fájl azáltal, hogy az AD FS-integráció beállításával](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Hogy kijavítva, hogy megakadályozta abban a rendelt felhasználók egy meglévő nyilvános IP-címet, amely korábban korábban hozzárendelve egy hálózati adapter vagy egy új hálózati adapter vagy a terheléselosztó a terheléselosztó.  

<!-- 2551834 - IS, ASDK --> 
- Ha bejelöli *áttekintése* a rendszergazdai vagy a felhasználói portált, a storage-fiókok a *Essentials* panelen most már megfelelően megjelenik-e a várt információkat. 

<!-- 2551834 - IS, ASDK --> 
- Ha bejelöli *címkék* a rendszergazdai vagy a felhasználói portált a storage-fiókok, az információt mostantól megfelelően megjelenik-e.

<!-- TBD - IS ASDK --> 
- Az Azure Stack jelen verziójában javítja a problémát, amely megakadályozta az OEM bővítménycsomagok illesztőprogram-frissítések alkalmazását.

<!-- 2055809- IS ASDK --> 
- A hibát, amely megakadályozza, hogy a számítási panelről virtuális gépek törlése, ha a virtuális gép létrehozása nem sikerült rögzíteni azt.  

<!--  2643962 IS ASDK -->  
- A riasztás *kevés a memória-kapacitás* hibásan már nem jelenik meg.

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures
Az Azure Stack a gazda-infrastruktúrát a Windows Server 2016 Server Core-telepítéseket használ. Ebben a kiadásban a következő Windows Server 2016 frissítéseket telepíti az infrastruktúra-kiszolgálók az Azure Stack: 
- [CVE – 2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE – 2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE – 2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE – 2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE – 2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE – 2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE – 2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

További információ a biztonsági rések, kattintson a fenti hivatkozásokat a, vagy tekintse meg a Microsoft Tudásbázis cikkei [4338814](https://support.microsoft.com/help/4338814) és [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

- Az Azure Stack telepítése [1805 frissítése](azure-stack-update-1805.md) az Azure Stack 1807 frissítés alkalmazása előtt.  Hiba történt a frissítés nélkül 1806.  

- Telepítse a legújabb elérhető [frissítést vagy gyorsjavítást verzió 1805](azure-stack-update-1805.md#post-update-steps).  
  > [!TIP]  
  > Fizessen elő a következő *RRS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Az Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

<!-- 2468613 - IS --> 
- Ez a frissítés telepítése során láthatja a címmel riasztások *hiba – a sablon typu FaultType UserAccounts.New hiányzik.*  Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. A frissítés telepítésének befejezése után ezek a riasztások automatikusan bezáródik.

<!-- 2489559 - IS --> 
- Ne kísérelje meg a frissítés telepítése során a virtuális gépek létrehozása. Frissítések kezelésével kapcsolatos további információkért lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md#plan-for-updates).

<!-- 2830461 - IS --> 
- Bizonyos körülmények között, ha egy frissítéshez a figyelmet, a megfelelő riasztás lehetséges, hogy nem hozható létre. A pontos állapota továbbra is megjelennek a portálon, és nem változik.

### <a name="post-update-steps"></a>Frissítés utáni lépések
Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információt a következő cikkeket, megtekintése, valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md). 
- [KB 4467061 – az Azure Stack gyorsjavítás az Azure Stack gyorsjavítás 1.1807.3.82](https://support.microsoft.com/help/4467061/)

<!-- 2933866 – IS --> A frissítés telepítése után megtekintheti a **javult a sikertelen frissítés telepítések állapotát.** Ez tartalmazhat, amelyek módosítják, hogy a két új állapot kategória előző frissítés telepítési hibák adatait. Az új állapot kategória *PreparationFailed*, és *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

- Az Azure Stack technikai dokumentáció a legfrissebb kiadás összpontosít. Portál változások között, mert megjelenő használata az Azure Stack portálok kissé eltérhetnek a dokumentációban talál. 

- Lehetővé teszi [nyisson egy új támogatási kérelmet a legördülő listából](azure-stack-manage-portals.md#quick-access-to-help-and-support) belül a felügyeleti portálon nem érhető el. Az Azure Stack integrált rendszerek, használja a következő hivatkozásra: [ https://aka.ms/newsupportrequest ](https://aka.ms/newsupportrequest).

<!-- 2931230 – IS  ASDK --> 
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

<!--2760466 – IS  ASDK --> 
- Amikor telepít egy új Azure Stack-környezet, amely ebben a verzióban fut, a riasztás azt jelzi, *aktiválás szükséges* nem jelenítik meg. [Az aktiválás](azure-stack-registration.md) marketplace szindikálási használatához szükség.  

<!-- TBD - IS ASDK --> 
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

<!-- 2403291 - IS ASDK --> 
- Lehet, hogy nincs használatát a vízszintes görgetősáv a rendszergazdai és felhasználói portál alján. Ha nem éri el a vízszintes görgetősáv, az útkövetés, keresse meg a portálon egy előző panelen válassza a panel neve, a tetején található navigációs listában megtekinteni kívánt használati a portál bal oldali.

  ![Webhely-navigációs](media/azure-stack-update-1804/breadcrumb.png)

<!-- TBD - IS --> 
- Ez nem lehetséges számítási és tárolási erőforrások megtekintése a felügyeleti portálon. A probléma oka hiba történt a frissítés sikeres helytelenül jelenti a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon segítségért a Microsoft ügyfél-támogatási szolgálathoz.

<!-- TBD - IS --> 
- Előfordulhat, hogy megjelenik egy üres irányítópult, a portálon. Szeretné használni az irányítópultot, kattintson a fogaskerék ikonra a portál jobb felső sarokban, és válassza **alapértelmezett beállítások visszaállítása**.

<!-- TBD - IS ASDK --> 
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

<!-- TBD - IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.


### <a name="health-and-monitoring"></a>Állapot és figyelés

<!-- TBD - IS -->
- Az alábbi riasztásokat ismételten megjelenik, és az Azure Stack rendszeren majd eltűnnek jelenhetnek meg:
   - *Infrastruktúra szerepkör-példány nem érhető el*
   - *Skálázási egység csomópont offline állapotban.*
   
  Futtassa a [Test-AzureStack](azure-stack-diagnostic-test.md) parancsmagot, hogy az infrastruktúra-szerepkör példányai állapotának ellenőrzése és egység csomópontok méretezése. Ha nincsenek problémák észlelhetők által [Test-AzureStack](azure-stack-diagnostic-test.md), ezek a riasztások figyelmen kívül hagyhatja. Ha problémát észlel, próbálja meg elindítani a szerepkörpéldány infrastruktúra vagy a csomópont a felügyeleti portálon vagy a PowerShell használatával.

<!-- 1264761 - IS ASDK -->  
- A riasztásokat a **egészségügyi vezérlő** összetevő, amely rendelkezik a következő adatokat:  

   #1 riasztás:
   - NAME: Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Állapotfigyelő vezérlő
   - Leírás: A health vezérlő szívverési képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.  

  Riasztási #2:
   - NAME: Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Állapotfigyelő vezérlő
   - Leírás: A health vezérlő tartalék képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.

  Mindkét riasztás biztonságosan figyelmen kívül hagyható, és automatikusan fog bezárása idővel.  


<!-- 2812138 | IS --> 
- Láthatja, hogy egy riasztás **tárolási** összetevő, amely rendelkezik a következő adatokat:

   - NAME: Storage szolgáltatás belső kommunikációs hiba  
   - SÚLYOSSÁG: kritikus  
   - ÖSSZETEVŐ: Storage  
   - Leírás: A Storage szolgáltatás belső kommunikációs hiba történt, amikor kéréseket küld az alábbi csomópontok.  

    A riasztás biztonságosan figyelmen kívül hagyható, de a riasztás lezárása manuálisan kell.

<!-- 2368581 - IS. ASDK --> 
- Az Azure Stack operátorait, ha kevés a szabad memória figyelmeztetést, és a bérlői virtuális gépek sikertelen üzembe helyezés egy **Fabric Virtuálisgép-létrehozási hiba**, lehetséges, hogy az Azure Stack-blokk esik a rendelkezésre álló memória. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobban megérteni az elérhető kapacitás a számítási feladatokhoz.


### <a name="compute"></a>Compute

<!-- 2724873 - IS --> 
- A PowerShell-parancsmagok használata esetén **Start-AzsScaleUnitNode** vagy **Stop-AzsScaleunitNode** skálázási egységek kezelése, indítása vagy leállítása a skálázási egység az első kísérlet sikertelen lehet. A parancsmag futtatása sikertelen, az első, futtassa a parancsmagot egy második alkalommal. A második Futtatás sikeres legyen, a művelet végrehajtásához. 

<!-- 2494144 - IS, ASDK --> 
- Amikor kiválasztja a virtuális gép üzembe helyezéséhez virtuálisgép-méretet, néhány F-sorozat Virtuálisgép-méretek nem láthatók a virtuális gép létrehozásakor a méret választó részeként. Az alábbi virtuális gépeinek-választójában jelenítse nem jelennek meg: *F8s_v2*, *F16s_v2*, *F32s_v2*, és *F64s_v2*.  
  Áthidaló megoldásként használja a következő módszerek egyikét virtuális gép üzembe helyezése. Az egyes módszerek kell adja meg a használni kívánt virtuális gép méretét.

  - **Az Azure Resource Manager-sablon:** egy sablont használ, amikor a *vmSize* egyenlő a használni kívánt Virtuálisgép-méretet a sablonban. Ha például a következő bejegyzés központi telepítéséhez használandó használó virtuális gép a *F32s_v2* mérete:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Az Azure CLI:** használhatja a [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) parancsot, és adja meg a virtuális gép méretét a paramétert, hasonló `--size "Standard_F32s_v2"`.

  - **PowerShell:** a PowerShell segítségével használható [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) a paraméterrel, amely meghatározza a virtuális gép méretét, hasonló `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

<!-- TBD - IS --> 
- Amikor létrehoz egy rendelkezésre állási csoportot a portálon a **új** > **számítási** > **rendelkezésre állási csoport**, csak hozhat létre egy rendelkezésre állási csoport egy tartalék tartomány és a frissítési tartomány 1. Áthidaló megoldásként egy új virtuális gép létrehozásakor, a rendelkezésre állási csoportot használja a Powershellt, CLI-t, vagy létrehozása a portálon.

<!-- TBD - IS ASDK --> 
- Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, hogy a DS sorozatú virtuális gép csatlakoztathat adatlemezek száma helytelen. DS sorozatú virtuális gépek, az Azure-konfiguráció lehetővé teszi tetszőleges számú adatlemezeket.

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

<!-- 1662991 IS ASDK --> 
- Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  

<!-- 2724961- IS ASDK --> 
- Amikor regisztrál a **Microsoft.Insight** előfizetési beállítások, az erőforrás-szolgáltató egy Windows virtuális gép létrehozása és a vendég operációs rendszer diagnosztikai engedélyezve van, a virtuális gépek – Áttekintés lap nem jelenik meg a mérőszámadatok. 

   Keresse meg a mérőszámadatokat, például a processzor-diagram a virtuális gép, lépjen a **metrikák** panel és a támogatott Windows VM show Vendég mérőszámok.

### <a name="networking"></a>Hálózat  

<!-- 1766332 - IS ASDK --> 
- A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

<!-- 1902460 - IS ASDK --> 
- Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

<!-- 16309153 - IS ASDK --> 
- A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

<!-- 2702741 -  IS ASDK --> 
- Nyilvános IP-címek, a dinamikus kiosztási módszer használatával üzembe helyezett nem biztos, hogy egy állítsa le és vonja vissza kiadása után megőrzi.

<!-- 2529607 - IS ASDK --> 
- Azure Stack során *titkos Elforgatás*, egy ideig, amely nyilvános IP-címek nem érhetők el 2 – 5 percet.

<!-- 2664148 - IS ASDK --> 
- Olyan esetekben, ahol a bérlői fér hozzá a virtuális gépek egy S2S VPN-alagút használatával akkor léphetnek fel egy olyan forgatókönyvet, ahol sikertelen csatlakozási kísérletek, ha a helyszíni alhálózati hozzá lett adva a helyi hálózati átjáró után átjárón már létre lett hozva. 


### <a name="sql-and-mysql"></a>SQL- és MySQL



<!-- No Number - IS, ASDK -->  
- Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** nevezze a Termékváltozat létrehozott SQL- és MySQL erőforrás-szolgáltatókat. 

<!-- TBD - IS --> 
- Konfigurációelemek létrehozása a kiszolgálókon, a fogadó SQL vagy MySQL csak az erőforrás-szolgáltató támogatott. A gazdagép-kiszolgálón létrehozott elemek nem az erőforrás-szolgáltató által létrehozott egy nem megfelelő állapot eredményezhet.  

<!-- TBD - IS -->
> [!NOTE]   
> Miután frissítette az Azure Stack ezen verziójára, akkor továbbra is használja az SQL- és MySQL erőforrás-szolgáltatókat korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és a MySQL amikor új kiadása elérhetővé válik. Azure Stack, például frissítések alkalmazása az SQL- és MySQL erőforrás-szolgáltatók egymás után. Például 1804 verzióját használja, ha először a alkalmazni verzió 1805, és frissítse a 1807.  
>  
> Ez a frissítés telepítésének nincs hatással az aktuális használatát a felhasználók által SQL vagy MySQL típusú erőforrás-szolgáltatók. 
> Az erőforrás-szolgáltatókat használ verziójától függetlenül adatbázisaikat a felhasználók adatait nem megszáradásukig, és elérhető marad.  

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

<!-- 2489178 - IS ASDK --> 
- Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.

<!-- TBD - IS ASDK --> 
- App Service-ben csak lesz üzembe helyezve a *alapértelmezett szolgáltatói előfizetés* jelenleg. 


### <a name="usage"></a>Használat  
<!-- TBD - IS ASDK --> 
- Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure Stack 1807 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="next-steps"></a>További lépések
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).  
