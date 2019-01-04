---
title: Azure Stack 1811 frissítése |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1811 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.date: 12/22/2018
ms.author: sethm
ms.reviewer: adepue
ms.openlocfilehash: da481a2b6a20b5b5dbd75a23aafffc0bc0a8fef9
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752758"
---
# <a name="azure-stack-1811-update"></a>Azure Stack 1811 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1811 csomag tartalmát. A frissítési csomag fejlesztései, javításokat és új funkciók az Azure Stack ezen verziója tartalmazza. Ez a cikk is ebben a kiadásban az ismert problémákat, és tartalmaz egy hivatkozást, így a frissítés letölthető. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat, és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1811 frissítés buildszáma **1.1811.0.101**.

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Ne felejtse el telepíteni a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) a 1809 1811 Azure Stack frissítése előtt.

> [!TIP]  
> Fizessen elő a következő *RRS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
> - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
> - Az Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...

### <a name="azure-stack-hotfixes"></a>Az Azure Stack-gyorsjavítások

- **1809**: [KB 4481548 – az Azure Stack gyorsjavítás 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nincs elérhető aktuális gyorsjavítást.

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A 1811 frissítés telepítése során gondoskodnia kell arról, hogy a felügyeleti portál összes példánya nincs megnyitva. A felhasználói portál nyitva maradhat, de be kell zárni a felügyeleti portálon.

- Készüljön fel az Azure Stack üzembe helyezés az Azure Stack-bővítmény állomás. Készítse elő a rendszert a használatával a következő útmutatást: [Azure stack-bővítmény gazdagép előkészítése](azure-stack-extension-host-prepare.md). 
 
- Telepítse a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) a 1809 1811 frissítése előtt.

- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő.  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Ha nem rendelkezik a bővítmény gazdagép követelmények teljesülnek, a `Test-AzureStack` kimenet a következő üzenet jelenik meg: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Az Azure Stack 1811 frissítéshez, hogy helyesen importálta a kötelező kiterjesztés gazdagép tanúsítványok az Azure Stack környezettel. A 1811 frissítés telepítésének folytatásához, importálnia kell a bővítmény gazdagép számára szükséges SSL-tanúsítványokat. Tekintse meg a tanúsítványok importálásához [ebben a szakaszban](azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Ha figyelmen kívül minden figyelmeztetést, és továbbra is dönt, hogy a 1811 frissítés telepítése, a frissítés sikertelen lesz a körülbelül egy óra elteltével a következő üzenettel:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Miután a kötelező kiterjesztés gazdagép tanúsítványok helyesen importálta, a felügyeleti portálról 1811 frissítés folytathatja. Azure Stack-operátorok, a skálázási egység karbantartási módba a frissítési folyamat alatt a Microsoft azt ajánlja, amíg egy hiba miatt a hiányzó bővítmény gazdagép tanúsítványok nem érinti meglévő számítási feladatok vagy szolgáltatások.  

    Ez a frissítés telepítése során az Azure Stack felhasználói portálon nem érhető el amíg folyamatban van a bővítmény állomás konfigurálása. A bővítmény gazdagép a konfiguráció legfeljebb 5 órát is igénybe vehet. Ez idő alatt a frissítés állapotának ellenőrzése, vagy indítsa el a sikertelen frissítés telepítési használatával [Azure Stack rendszergazdai PowerShell-lel vagy a kiemelt végponthoz](azure-stack-monitor-update.md).

## <a name="new-features"></a>Új funkciók

A frissítés tartalmazza a következő új funkciókat és fejlesztéseket az Azure Stack:

- Ebben a kiadásban a [bővítmény gazdagép](azure-stack-extension-host-prepare.md) engedélyezve van. A bővítmény gazdagép hálózati integráció leegyszerűsíti, és növeli a biztonságot az Azure Stack.

- Támogatás hozzáadva az eszközhitelesítés az Active Directory összevont szolgáltatások (AD FS), ha különösen az Azure CLI használatával. További információkért lásd: [használata API-verzióprofilok az Azure CLI-vel az Azure Stackben](./user/azure-stack-version-profiles-azurecli2.md)

- A szolgáltatásnevek használatával ügyfélkódot az Active Directory összevont szolgáltatások (AD FS) támogatása. További információkért lásd: [az AD FS egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Ebben a kiadásban a következő Azure Storage szolgáltatás API-verziók támogatásával bővült: **2017-07-29**, **2017-11-09**. Támogatás is hozzá a következő Azure Storage Resource Provider API-verziók: **2016-05-01**, **2016-12-01**, **2017-06-01**, és **2017-10-01**. További információkért lásd: [Azure Stack-tároló: Különbségek és szempontok](./user/azure-stack-acs-differences.md).

- Új privileged végpont parancsok frissítése és az AD FS szolgáltatás alapelvek eltávolítása. További információkért lásd: [az AD FS egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Hozzáadott új méretezési egység csomópont műveleteket, amelyek lehetővé teszik az Azure Stack operátorait indítása, leállítása, és állítsa le a skálázási egység csomópont. További információkért lásd: [csomópont kapcsolatos műveletek méretezése az Azure Stack](azure-stack-node-actions.md).

- Hozzá egy új régióban Tulajdonságok panel, amely a környezet a regisztrációs adatokat jeleníti meg. Ez az információ kattintva megtekintheti a **régiók kezelése** a felügyeleti portálon az alapértelmezett irányítópulton lévő csempére, majd kiválasztja az **tulajdonságok**.

- A BMC-hitelesítő adat frissítése a felhasználónevét és jelszavát, a fizikai gépek folytatott kommunikáció során használt új kiemelt végponthoz parancs hozzáadva. További információkért lásd: [az alaplapi felügyeleti vezérlő Update \(BMC) hitelesítő adatok](azure-stack-rotate-secrets.md).

- Az Azure ütemterve, ha hozzáférést a Súgó és támogatás ikonra (kérdőjel) hozzáadva a rendszergazdai és felhasználói portálok, hasonló érhető el az Azure Portal jobb felső sarkában.

- Hozzáadott egy továbbfejlesztett Marketplace felügyeleti élmény a leválasztott számára. A feltöltési folyamat Piactéri elem közzététele a leválasztott környezetben egyszerűsödött, egy lépéssel a lemezkép és a piactér csomag feltöltése külön-külön helyett. A feltöltött termék is meg fognak jelenni a Marketplace-en felügyeleti panel. További információkért lásd: [ebben a szakaszban](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher). 

- Ebben a kiadásban csökkenti a szükséges karbantartási időszakot titkos Elforgatás során csak a külső tanúsítványok elforgatásának hozzáadásával [Azure Stack titkos Elforgatás](azure-stack-rotate-secrets.md).

- [Az Azure Stack PowerShell](azure-stack-powershell-install.md) 1.6.0-s verziójának frissült. A frissítés az Azure Stackben is tartalmaz a storage szolgáltatással kapcsolatos új funkciókkal. További információkért tekintse meg a kibocsátási megjegyzések a a [Azure Stack felügyeleti modul a PowerShell-galériából a 1.6.0-s](https://www.powershellgallery.com/packages/AzureStack/1.6.0) frissítése vagy az Azure Stack PowerShell telepítésével kapcsolatos információkért lásd: [PowerShell telepítése Az Azure Stack](azure-stack-powershell-install.md).

- A felügyelt lemezek mostantól alapértelmezés szerint engedélyezve van az Azure Stack portálon használó virtuális gépek létrehozásakor. Tekintse meg a [ismert problémák](#known-issues-post-installation) a Managed Disks Virtuálisgép-létrehozási hibák elkerülése érdekében szükséges további lépéseket a következő szakaszban.

- Ebben a kiadásban bevezeti a riasztás **javítási** az Azure Stack-operátori műveletek. Néhány 1811 riasztásokat biztosít egy **javítási** gombra a figyelmeztető kiválasztható a probléma megoldásához. További információkért lásd: [figyelni és riasztásokat az Azure Stackben](azure-stack-monitor-health.md).

## <a name="fixed-issues"></a>Hibák kijavítva:

<!-- TBD - IS ASDK --> 
- Kijavítva, amelyben a nyilvános IP-használatának mérési adatait mutatott azonos **EventDateTime** érték helyett minden egyes rekordja esetében a **TimeDate** , amely megjeleníti a rekord létrehozásának stamp. Ezek az adatok most már használhatja a nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.

<!-- 3099544 – IS, ASDK --> 
- Kijavítva a hiba akkor történt, amikor egy új virtuális gép (VM) az Azure Stack portal használatával hoz létre. A virtuális gép méretének okozott a **USD/hónap** megjelenítendő oszlop egy **nem érhető el** üzenet. Már nem jelenik meg ebben az oszlopban; megjelenítés, a virtuális gép díjszabási oszlop nem támogatott az Azure Stackben.

<!-- 2930718 - IS ASDK --> 
- Kijavítva, amelyben a felügyeleti portálon bezárja a panelt, és kattintson a után bármely felhasználói előfizetés részleteinek elérésekor **legutóbbi**, a felhasználói előfizetés neve ne jelenjenek meg. Most már megjelenik a felhasználói előfizetés neve.

<!-- 3060156 - IS ASDK --> 
- Javítva lett a rendszergazda és a felhasználói portálon egy probléma: a portál beállításainak kattint, és kiválasztja **összes beállítás és saját irányítópult törlése** volt a várt módon működik, és a egy hibaértesítésre zobrazilo. Ez a beállítás most már megfelelően működik.

<!-- 2930799 - IS ASDK --> 
- Kijavítva az a rendszergazda és a felhasználói portálon: alatt **minden szolgáltatás**, az eszköz **DDoS potection-tervek** nem megfelelően szerepel. Ez nem áll rendelkezésre, az Azure Stackben. Az eredményablakban el lett távolítva.
 
<!--2760466 – IS  ASDK --> 
- Kijavítva a hiba akkor történt, amikor telepítette az új Azure Stack-környezet, amelyben a riasztás azt jelzi, **aktiválás szükséges** nem jelennek meg. Most már megfelelően jelenik meg.

<!--1236441 – IS  ASDK --> 
- Kijavítva, amely ebben az esetben a felhasználói csoport számára az RBAC-szabályzatok alkalmazása, ADFS használata esetén.

<!--3463840 - IS, ASDK --> 
- Kijavítva infrastruktúra biztonsági mentése egy nem érhető el a nyilvános VIP-hálózat fájlkiszolgálót miatt meghiúsul. Javítás a biztonsági mentési infrastruktúra-szolgáltatás vissza helyezi a nyilvános infrastruktúra-hálózathoz. Ha a legújabb telepített [Azure Stack-gyorsjavítás a 1809](#azure-stack-hotfixes) , címek, a probléma, a 1811 frissítés nem hajt végre semmilyen további módosításokat végezhet. 

<!-- 2967387 – IS, ASDK --> 
- Javítva lett egy probléma, amelyben a fiókot, amellyel jelentkezzen be az Azure Stack rendszergazdai vagy a felhasználói portál néven **azonosítatlan felhasználó**. Ez az üzenet volt jelenik meg, ha a fiók nincs vagy egy **első** vagy **utolsó** megadott név.   

<!--  2873083 - IS ASDK --> 
- Kijavítva a, mely a portál segítségével hozzon létre egy virtuálisgép-méretezési csoportot (VMSS) okozott a **példány mérete** nem töltődik be megfelelően az Internet Explorer használata esetén a legördülő listából. Ez a böngésző most már megfelelően működik.  

<!-- 3190553 - IS ASDK -->
- Kijavítva, amely jelzi, hogy az infrastruktúra szerepkör-példány nem érhető el, vagy skálázási egység csomópont offline állapotban volt zajos riasztások jönnek létre.

## <a name="changes"></a>Módosítások

- Új módon lehet megnézni és szerkeszteni a kvóták csomag 1811 verziójában jelent meg. További információkért lásd: [megtekintéséhez egy már létező kvóta](azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Biztonsági fejlesztések az ezt a frissítést a címtár-szolgáltatási szerepkör biztonsági másolat méretének növekedését eredményezi. Frissítve a méretezési útmutató a külső tárhelyen találhatók, lásd: a [infrastruktúra backup – dokumentáció](azure-stack-backup-reference.md#storage-location-sizing). Ez a módosítás végrehajtásához mérete nagyobb adatforgalom miatt a biztonsági mentés hosszabb időt eredményez. Ez a módosítás hatással van az integrált rendszerekről. 

- A BitLocker helyreállítási kulcsok lekéréséhez a meglévő EGP parancsmag 1811, a Get-AzsCsvsRecoveryKeys a Get-AzsRecoveryKeys átnevezése. A BitLocker helyreállítási kulcsok lekéréséhez, hogy miként további információkért lásd: [útmutatást nyújt a kulcsok lekéréséhez](azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Ez a frissítés a következő biztonsági frissítéseket telepíti:  

- [CVE – 2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE – 2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE – 2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE – 2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE – 2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE – 2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE – 2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE – 2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE – 2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE – 2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE – 2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE – 2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE – 2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE – 2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE – 2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE – 2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE – 2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE – 2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE – 2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)


További információ a biztonsági rések, kattintson a fenti hivatkozásokat a, vagy tekintse meg a Microsoft Tudásbázis cikkei [4467684](https://support.microsoft.com/help/4467684).

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- A 1811 telepítése során frissíteni, győződjön meg arról, hogy a felügyeleti portál összes példánya nincs megnyitva ebben az időszakban. A felhasználói portál nyitva maradhat, de be kell zárni a felügyeleti portálon.

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md), ha a **AzsInfraRoleSummary** vagy a **AzsPortalApiSummary** teszt sikertelen, a rendszer felszólítja a Futtatás  **Test-AzureStack** együtt a `-Repair` jelzőt.  Ha ezt a parancsot futtatta, a következő hibaüzenettel meghiúsul:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  A probléma egy későbbi kiadásban lesz kijavítva.

- A 1811 frissítés telepítése során az Azure Stack portálon nem érhető el amíg folyamatban van a bővítmény állomás konfigurálása. A bővítmény gazdagép a konfiguráció legfeljebb 5 órát is igénybe vehet. Ez idő alatt a frissítés állapotának ellenőrzése, vagy indítsa el a sikertelen frissítés telepítési használatával [Azure Stack rendszergazdai PowerShell-lel vagy a kiemelt végponthoz](azure-stack-monitor-update.md). 

- A 1811 frissítés telepítése során előfordulhat, hogy a felhasználói portál irányítópultján nem érhető el, és lehet, hogy a testreszabások elveszett. Az irányítópult visszaállíthatja az alapértelmezett beállítás a portálbeállítások megnyitásával, majd válassza a frissítés befejezése után **alapértelmezett beállítások visszaállítása**.

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md), megjelenik egy figyelmeztető üzenet az alaplapi felügyeleti vezérlő (BMC). Biztonságosan figyelmen kívül hagyhatja ezt a figyelmeztetést.

- <!-- 2468613 - IS --> Ez a frissítés telepítése során láthatja a címmel riasztások `Error – Template for FaultType UserAccounts.New is missing.` biztonságosan figyelmen kívül hagyhatja ezeket a riasztásokat. A riasztás automatikusan bezáródik a frissítés a telepítés befejezése után.

- <!-- 3139614 | IS --> Ha már telepített egy frissítés az Azure Stack, az OEM, a **frissítés érhető el** értesítési nem jelenhet meg az Azure Stack rendszergazdai portálon. A Microsoft update telepítéséhez töltse le és importálja manuálisan az itt található utasításokat követve [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Frissítés utáni lépések

- Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: [gyorsjavítások](#hotfixes), valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).  

- Az adatok rest titkosítási kulcsok lekéréséhez és tárolja azokat biztonságosan kívül az Azure Stack üzemelő példányához. Kövesse a [útmutatást nyújt a kulcsok lekéréséhez](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve. 

<!-- 2931230 – IS  ASDK --> 
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

<!-- TBD - IS ASDK --> 
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

<!-- TBD - IS ASDK --> 
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és ezután törölje a felhasználói előfizetések.

<!-- TBD - IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként használja [engedélyek ellenőrzése érdekében PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Állapot és figyelés

<!-- 1264761 - IS ASDK -->  
- A riasztásokat a **egészségügyi vezérlő** összetevő, amely rendelkezik a következő adatokat:  

    - #1 riasztás:
       - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
       - SÚLYOSSÁG: Figyelmeztetés
       - ÖSSZETEVŐ: Vezérlő állapota
       - LEÍRÁS: A health vezérlő szívverési képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.  

    - Riasztási #2:
       - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
       - SÚLYOSSÁG: Figyelmeztetés
       - ÖSSZETEVŐ: Vezérlő állapota
       - LEÍRÁS: A health vezérlő tartalék képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.

    Mindkét riasztás biztonságosan figyelmen kívül hagyhatja. Ezek automatikusan megszűnik idővel.  

### <a name="compute"></a>Compute

- Amikor létrehozza az új Windows virtuális gép (VM), a **beállítások** panel megköveteli, hogy a folytatáshoz válassza ki a nyilvános bejövő port. 1811 Ez a beállítás megadása kötelező, de nem lesz hatása. Ennek az oka a funkció működéséhez az Azure-tűzfal, amely az Azure Stackben není implementována. Választhat **nem nyilvános bejövő portok**, vagy az egyéb beállításokat a virtuális gép létrehozásának folytatásához. A beállítás nem lesz hatása.

<!-- 3235634 – IS, ASDK -->
- Virtuális gépek telepítéséhez tartalmazó méretű egy **v2** utótag; például **Standard_A2_v2**, adja meg, mint az utótag **Standard_A2_v2** (kis v). Ne használjon **Standard_A2_V2** (nagybetűs V). Ez a globális Azure-ban működik, és az Azure Stacken inkonzisztencia.

<!-- 2869209 – IS, ASDK --> 
- Használatakor a [ **Add-AzsPlatformImage** parancsmag](/powershell/module/azs.compute.admin/add-azsplatformimage), kell használnia a **- OsUri** paramétert, a tárfiók URI, ahol fel a rendszer a lemezen. Ha a lemez a helyi elérési utat használ, a parancsmag futtatása sikertelen, a következő hibával: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Ha a portálon hozhat létre virtuális gépeket (VM) a prémium szintű virtuális gép méretét (DS, Ds_v2, FS, FSv2) használ, a virtuális gép létrejött, a standard szintű storage-fiókban. A standard szintű tárfiók-létrehozás nem befolyásolja, funkcionálisan IOPs, vagy számlázási. Biztonságosan figyelmen kívül hagyhatja a figyelmeztetést, amely szerint: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- A virtual machine scale set (VMSS) létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzembe helyezéshez használt egy másik operációs rendszert, vagy egy Azure Resource Manager-sablonnal, adjon meg egy másik CentOS rendszerképet, amely szerint a marketplace-ről üzembe helyezés előtt lett letöltve a operátor.  

<!-- 2724873 - IS --> 
- A PowerShell-parancsmagok használata esetén **Start-AzsScaleUnitNode** vagy **Stop-AzsScaleunitNode** skálázási egységek kezelése, indítása vagy leállítása a skálázási egység az első kísérlet sikertelen lehet. A parancsmag futtatása sikertelen, az első, futtassa a parancsmagot egy második alkalommal. A második Futtatás sikeresen kell végrehajtani a műveletet. 

<!-- TBD - IS ASDK --> 
- Egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, ha lehetővé teszik az üzembe helyezési időtúllépés helyett fel, vagy törölje a virtuális Gépet, a folyamat leállítása érdekében.  

<!-- 1662991 IS ASDK --> 
- Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  

<!-- 2724961- IS ASDK --> 
- Amikor regisztrál a **Microsoft.Insight** az előfizetés beállításait, az erőforrás-szolgáltató és a Windows virtuális gép létrehozása a vendég operációs rendszer diagnosztikai engedélyezve van, a Processzorhasználat (%) diagramon a virtuális gépek – Áttekintés lapon does show mérőszámadatokat.

   Keresse meg a mérőszámadatokat, például a processzor-diagram a virtuális gép, lépjen a **metrikák** ablakot, és a támogatott Windows VM show Vendég mérőszámok.

<!-- 3507629 - IS, ASDK --> 
- Felügyelt lemezeket hoz létre két új [kvótatípusok számítási](azure-stack-quota-types.md#compute-quota-types) korlátozhatja a felügyelt lemezek, amelyek kioszthatóak maximális kapacitását. Alapértelmezés szerint a 2048 GiB az egyes felügyelt lemezek kvóta van lefoglalva. Azonban előfordulhat, hogy problémák merülnek fel a következő:

   - Kvóták 1808 frissítés előtt létrehozott a Managed Disks kvóta jelennek meg 0 értéket a felügyeleti portálon, bár 2048 GiB le van foglalva. Növelheti vagy csökkentheti a tényleges igényeinek és az újonnan beállított értéket kvótaérték felülbírálja a 2048 GiB alapértelmezett.
   - Ha a kvóta értékének 0-ra frissít, egyenértékű, 2048 GiB alapértelmezett értékét. A probléma megoldásához állítsa a kvótaérték 1.

<!-- TBD - IS ASDK --> 
- Frissítse a 1811 alkalmazása után, a következő problémák léphetnek a felügyelt lemezekkel rendelkező virtuális gépek üzembe helyezésekor:

   - Ha az előfizetés korábban jött létre a 1808 frissítése, a felügyelt lemezekkel rendelkező virtuális gép üzembe helyezése egy belső hiba miatt sikertelen lehet. A hiba elhárításához kövesse ezeket a lépéseket minden egyes előfizetés esetén:
      1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Válassza ki **erőforrás-szolgáltatók**, majd **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
      2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)**, és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
   - Egy több-bérlős környezet van beállítva, ha a virtuális gépek üzembe helyezéséhez egy adott előfizetéshez tartozó és a Vendég címtár egy belső hiba miatt meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket a [Ez a cikk](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) újrakonfigurálása a Vendég címtárak mindegyike.

- Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem teszi lehetővé, hogy jelentkezzen be az SSH-kulcsok használata. Áthidaló megoldásként használja a Linux-bővítményt a Virtuálisgép-hozzáférés SSH-kulcsok megvalósításához a kiépítés után, vagy jelszóalapú hitelesítés használatára.

### <a name="networking"></a>Hálózat  

<!-- 1766332 - IS ASDK --> 
- A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

<!-- 1902460 - IS ASDK --> 
- Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után a rendszer elutasítja.

<!-- 16309153 - IS ASDK --> 
- A DNS-kiszolgáló beállítása a létrehozott virtuális hálózaton **automatikus**, egy egyéni DNS-kiszolgálónak módosítása sikertelen lesz. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

<!-- 2529607 - IS ASDK --> 
- Azure Stack során *titkos Elforgatás*, egy ideig, amely nyilvános IP-címek nem érhetők el 2 – 5 percet.

<!-- 2664148 - IS ASDK --> 
-   Olyan esetekben, ahol a bérlői fér hozzá a virtuális gépek egy S2S VPN-alagút használatával akkor léphetnek fel egy olyan forgatókönyvet, ahol sikertelen csatlakozási kísérletek, ha a helyszíni alhálózati hozzá lett adva a helyi hálózati átjáró már az átjáró létrehozása után. 

- Az Azure Stack portálon Ha módosít egy statikus IP-címet az IP-konfiguráció a Virtuálisgép-példányhoz csatolt hálózati adapterhez kötött látni fogja egy figyelmeztető üzenet arról, hogy 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Biztonságosan figyelmen kívül hagyhatja ezt az üzenetet; az IP-cím megváltozik, akkor is, ha a Virtuálisgép-példány nem indítja újra.

- A portálon a a **hálózati tulajdonságok** panel egy hivatkozást a **érvényes biztonsági szabályokat** minden hálózati adapterhez. Ha bejelöli ezt a hivatkozást, egy új panel nyílik meg a hibaüzenetet megjelenítő `Not Found.` Ez a hiba akkor fordul elő, mert az Azure Stackben még nem támogatja **érvényes biztonsági szabályokat**.

- A portálon, ha egy bejövő biztonsági szabály felvétele, és válassza a **Szolgáltatáscímke** forrásaként, több lehetőség megjelennek a **forráscímke** listája, amelyek nem érhetők el az Azure Stackhez. Érvényes, az Azure Stack csak lehetőségek a következők:

    - **Az Internet**
    - **Virtuális hálózat**
    - **AzureLoadBalancer**
  
    A többi beállítást az Azure Stackben forrás nem támogatottak. Hasonlóképpen ha adjon hozzá egy kimenő biztonsági szabályt, és válassza **Szolgáltatáscímke** célhelyeként, ugyanezt a listát lehetőségei **forráscímke** jelenik meg. A csak érvényes beállítások ugyanazok, mint a **forráscímke**, amint az az előző listában.

- A **New-AzureRmIpSecPolicy** PowerShell-parancsmag nem támogatja a beállítás **DHGroup24** számára a `DHGroup` paraméter.

### <a name="infrastructure-backup"></a>Infrastruktúra biztonsági mentése

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Miután engedélyezte az automatikus biztonsági mentést, a Feladatütemező szolgáltatás hiányzóra tiltva váratlanul. A biztonsági mentési hálózativezérlő-szolgáltatás észleli, hogy az automatikus biztonsági mentés le vannak tiltva, és figyelmeztetés a felügyeleti portálon. Ez a figyelmeztetés várható, ha az automatikus biztonsági mentés le vannak tiltva. 
    - OK: A probléma van a szolgáltatásban, amely az ütemezési konfiguráció elvesztését eredményezi egy hiba miatt. Ez a hiba nem változik, a tárolási helyét, a felhasználónevet, a jelszó vagy a titkosítási kulcs.   
    - Szervizelési: A probléma megoldásához nyissa meg a biztonsági mentési infrastruktúra erőforrás-szolgáltató a biztonsági mentés vezérlő beállítások panelre, és válassza **engedélyezze az automatikus biztonsági mentés**. Ellenőrizze, hogy a kívánt gyakoriság és megőrzési időszak beállítása.
    - Előfordulás: Alacsony 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- A storage erőforrás-szolgáltatót regisztrálnia kell, az előfizetés az első Azure-függvény létrehozása előtt.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1811 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload). 

Csak csatlakoztatott forgatókönyvekben Azure Stack üzemelő példányok rendszeres időnként ellenőrzik egy biztonságos végpontot, és automatikusan értesíti, ha egy frissítés érhető el a felhőben. További információkért lásd: [frissítések kezelése az Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>További lépések

- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).  
