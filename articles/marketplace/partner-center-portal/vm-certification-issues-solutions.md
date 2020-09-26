---
title: A virtuális gép hitelesítése – problémák és megoldások
description: Ez a cikk a virtuálisgép-lemezképekkel kapcsolatos gyakori hibaüzeneteket ismerteti. Emellett a kapcsolódó megoldásokat is tárgyalja
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 06/16/2020
ms.openlocfilehash: 6d7f9ccd1c87b6105988a1f5d23700cb58693062
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296450"
---
# <a name="issues-and-solutions-during-virtual-machine-certification"></a>Problémák és megoldások a virtuális gépek minősítése során 

Amikor közzéteszi a virtuális gép (VM) rendszerképét az Azure Marketplace-en, az Azure csapata ellenőrzi, hogy a rendszerindítási, a biztonsági és az Azure-kompatibilitást biztosítja-e. Ha a kiváló minőségű tesztek bármelyike meghiúsul, a közzététel sikertelen lesz, és hibaüzenet jelenik meg, amely leírja a problémát.

Ez a cikk a virtuálisgép-rendszerkép közzétételekor, valamint a kapcsolódó megoldásokkal kapcsolatos gyakori hibaüzeneteket ismerteti.

> [!NOTE]
> Ha kérdése vagy visszajelzése van a fejlesztéssel kapcsolatban, forduljon a [partner Center támogatási szolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Jóváhagyott alaprendszerkép

Ha olyan kérést küld, amely a rendszerkép frissítéssel való újbóli közzétételét kéri, akkor előfordulhat, hogy a részleges ellenőrzés tesztelési esete meghiúsul. Ha nem sikerül, a rendszerkép nem lesz jóváhagyva.

Ez a hiba akkor fordul elő, ha egy másik közzétevőhöz tartozó alaprendszerképet használ, és frissítette a rendszerképet. Ebben az esetben a rendszerkép közzététele nem engedélyezett.

A probléma megoldásához kérje le a rendszerképet az Azure Marketplace-ről, és végezze el a módosítását. További információkért tekintse át a következő cikkeket:

- [Linux-rendszerképek](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-rendszerképek](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

> [!Note]
> Ha olyan linuxos alaprendszerképet használ, amely nem a piactéren alapul, az első partíciót 2048 KB-ra lehet ellensúlyozni. Ez lehetővé teszi, hogy a formázatlan terület felhasználható legyen új számlázási adatok hozzáadására, és lehetővé teszi az Azure számára a virtuális gép közzétételét a piactéren.  

## <a name="vm-extension-failure"></a>VM-bővítmény hibája

Ellenőrizze, hogy a rendszerkép támogatja-e a virtuálisgép-bővítményeket.

A virtuálisgép-bővítmények engedélyezéséhez tegye a következőket:

1. Válassza ki a linuxos virtuális gépet.
1. Lépjen a **diagnosztikai beállítások menüpontra**.
1. Az alapmátrixok engedélyezéséhez frissítse a **Storage-fiókot**.
1. Kattintson a **Mentés** gombra.

   ![Vendégszintű monitorozás engedélyezése](./media/vm-certification-issues-solutions-1.png)

A virtuálisgép-bővítmények megfelelő aktiválásának ellenőrzéséhez tegye a következőket:

1. A virtuális gépen válassza a virtuálisgép- **bővítmények** fület, majd ellenőrizze a **Linux diagnosztikai bővítmény**állapotát.
    * Ha az állapot kiosztása *sikeres*volt, a bővítmények tesztelési esete lett átadva.  
    * Ha az állapot *kiépítés sikertelen*, a bővítmények tesztelési esete meghiúsult, és be kell állítania a megerősített jelzőt.

      ![A kiépítés sikerességét bemutató képernyőkép](./media/vm-certification-issues-solutions-2.png)

      Ha a virtuálisgép-bővítmény nem sikerül, tekintse meg a következő témakört: a [Linux diagnosztikai bővítmény használata a metrikák és naplók figyelésére](../../virtual-machines/extensions/diagnostics-linux.md) az engedélyezéshez. Ha nem szeretné, hogy a virtuálisgép-bővítmény engedélyezve legyen, forduljon a támogatási csapathoz, és kérje meg, hogy tiltsa le.

## <a name="vm-provisioning-issue"></a>VIRTUÁLIS gépek kiépítési hibája

Győződjön meg arról, hogy a virtuális gép üzembe helyezési folyamatát szigorúan követte az ajánlat elküldése előtt. Ha meg szeretné tekinteni a virtuális gép kiépítési JSON-formátumát, tekintse meg az Azure-beli [virtuális gép rendszerképekkel kapcsolatos minősítését](azure-vm-image-certification.md).

A kiépítési problémák a következő meghibásodási helyzetekben lehetnek:

|Használati eset|Hiba|Ok|Megoldás|
|---|---|---|---|
|1|Érvénytelen virtuális merevlemez (VHD)|Ha a VHD-láblécben megadott cookie-érték helytelen, a VHD-fájl érvénytelennek tekintendő.|Hozza létre újra a lemezképet, és küldje el a kérést.|
|2|Érvénytelen blob-típus|A virtuális gép kiépítés meghiúsult, mert a használt blokk egy oldal típusa helyett blob típusú.|Hozza létre újra a lemezképet, és küldje el a kérést.|
|3|Kiépítési időtúllépés vagy nem megfelelően általánosítva|Probléma van a virtuális gépek általánosításával.|Hozza létre újra a rendszerképet az általánosítással, és küldje el a kérelmet.|

> [!NOTE]
> A virtuális gépek általánosításával kapcsolatos további információkért lásd:
> - [Linux-dokumentáció](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Windows-dokumentáció](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Windows rendszerhez készült szoftverek megfelelősége

Ha a Windows-rendszerképre vonatkozó kérelmet a szoftver megfelelőségi problémája miatt elutasította a rendszer, előfordulhat, hogy a telepített SQL Server-példánnyal létrehozott egy Windows-rendszerképet, és nem az Azure Marketplace-ről származó megfelelő SQL-verziót vette át.

Ne hozzon létre saját Windows-rendszerképet, amelyben az SQL Server telepítve van. Ehelyett használja a jóváhagyott SQL alaplemezképeket (Enterprise/standard/web) az Azure Marketplace-ről.

Ha a Visual studiót vagy bármely Office-licenccel rendelkező terméket megpróbál telepíteni, az előzetes jóváhagyáshoz forduljon a támogatási csoporthoz.

A jóváhagyott alap kiválasztásáról további információt az Azure-beli [virtuális gépek technikai eszközeinek létrehozása](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)című témakörben talál.

## <a name="tool-kit-test-case-execution-failed"></a>Az eszközkészlet tesztelési esetének végrehajtása nem sikerült 

A Microsoft minősítési eszközkészlet segítséget nyújt a tesztelési esetek futtatásához, és annak ellenőrzéséhez, hogy a VHD vagy a rendszerkép kompatibilis-e az Azure-környezettel.

Töltse le a [Microsoft minősítési eszközkészletet](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Linux-tesztelési esetek

A következő táblázat felsorolja az eszközkészlet által futtatott Linux-tesztelési eseteket. A teszt érvényesítése a leírásban van megadva.

|Használati eset|Teszteset|Leírás|
|---|---|---|
|1|Bash-előzmények|A rendszer a virtuális gép rendszerképének létrehozása előtt törli a bash-előzmények fájljait.|
|2|Linux-ügynök verziója|Telepíteni kell az Azure Linux Agent 2.2.41 vagy újabb verzióját.|
|3|Szükséges kernel-paraméterek|Ellenőrzi, hogy a következő kernel paraméterek vannak-e beállítva: <br>konzol = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Partíció cseréje az operációsrendszer-lemezen|Ellenőrzi, hogy a swap partíciók nincsenek-e létrehozva az operációsrendszer-lemezen.|
|5|Rendszerlemez gyökérkönyvtára|Hozzon létre egyetlen legfelső szintű partíciót az operációsrendszer-lemez számára.|
|6|OpenSSL-verzió|Az OpenSSL verziójának v 0.9.8 vagy újabb verziójúnak kell lennie.|
|7|Python-verzió|A Python 2,6-es vagy újabb verziója kifejezetten ajánlott.|
|8|Ügyfél élettartamának intervalluma|ClientAliveInterval beállítása 180-re. Az alkalmazásra van szükség, amely 30 és 235 között állítható be. Ha engedélyezi az SSH-t a végfelhasználók számára, ezt az értéket a magyarázatnak megfelelően kell beállítani.|
|9|Operációs rendszer architektúrája|Kizárólag a 64 bites operációs rendszerek támogatottak.|
|10|Automatikus frissítés|Meghatározza, hogy engedélyezve van-e a Linux-ügynök automatikus frissítése.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Gyakori hibák találhatók a korábbi tesztelési esetek végrehajtásakor

A következő táblázat felsorolja a korábbi tesztelési esetek végrehajtása során talált gyakori hibákat:
 
|Használati eset|Teszteset|Hiba|Megoldás|
|---|---|---|---|
|1|Linux-ügynök verziója – tesztelési eset|A Linux-ügynök minimális verziója 2.2.41 vagy újabb. Ezt a követelményt a 2020. május 1. óta kötelező megadni.|Frissítse a Linux-ügynök verzióját, és 2,241-es vagy újabb verziójának kell lennie. További információt a [Linux-ügynök verziójának frissítését ismertető oldalon](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)talál.|
|2|Bash-előzmények tesztelési esete|Hibaüzenet jelenik meg, ha az elküldött képen a bash-előzmények mérete meghaladja az 1 kilobájtot (KB). A méret 1 KB-ra van korlátozva, így biztosítható, hogy a rendszer ne rögzítse az esetlegesen bizalmas adatokat a bash-előzmények fájljában.|A probléma megoldásához csatlakoztassa a virtuális merevlemezt bármely más működő virtuális géphez, és végezze el a kívánt módosításokat (például törölje a *. bash* -előzmények fájljait), hogy csökkentse a méretet 1 KB-nál kisebb vagy egyenlő értékre.|
|3|Szükséges kernel-paraméterek tesztelési esete|Ez a hibaüzenet akkor jelenik meg, ha a **konzol** értéke nem **ttyS0**értékre van állítva. A következő parancs futtatásával győződjön meg arról, hogy:<br>`cat /proc/cmdline`|Állítsa be a **konzol** értékét a **ttyS0**értékre, majd küldje el újra a kérelmet.|
|4|ClientAlive intervalluma – tesztelési eset|Ha az eszközkészlet eredménye sikertelen eredményt ad a tesztelési esethez, a **ClientAliveInterval**nem megfelelő értékkel rendelkezik.|Állítsa a **ClientAliveInterval** értéket a 235 értéknél kisebbre vagy azzal egyenlőre, majd küldje el újra a kérelmet.|

### <a name="windows-test-cases"></a>Windows-tesztelési esetek

A következő táblázat felsorolja az eszközkészlet által futtatott Windows-tesztelési eseteket, valamint a tesztek ellenőrzésének leírását:

|Használati eset |Tesztelési esetek|Leírás|
|---|---|---|---|
|1|Operációs rendszer architektúrája|Az Azure csak a 64 bites operációs rendszereket támogatja.|
|2|Felhasználói fióktól való függőség|Az alkalmazás végrehajtása nem függhet a rendszergazdai fióktól.|
|3|Feladatátvevő fürt|A Windows Server feladatátvételi fürtszolgáltatás még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|4|IPV6|Az IPv6 még nem támogatott az Azure-környezetben. Az alkalmazás nem függ ettől a szolgáltatástól.|
|5|DHCP|Dynamic Host Configuration Protocol kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|6|Hyper-V|A Hyper-V kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|7|Távelérés|A távelérés (közvetlen hozzáférés) kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|8|Rights Management szolgáltatások|Rights Management szolgáltatások. A kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|9|Központi Windows-telepítési szolgáltatások|Központi Windows-telepítési szolgáltatások. A kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|10|BitLocker meghajtótitkosítás|A BitLocker meghajtótitkosítás az operációs rendszer merevlemezén nem támogatott, de adatlemezeken is használható.|
|11|Internet Storage-névkiszolgáló|Az Internet Storage-névkiszolgáló szolgáltatás még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|12|Többutas I/O|Többutas I/O. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|13|Hálózati terheléselosztás|Hálózati terheléselosztás. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|14|Peer Name Resolution Protocol|Peer Name Resolution Protocol. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|15|SNMP-szolgáltatások|A Simple Network Management Protocol (SNMP) szolgáltatások szolgáltatás még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|16|Windows Internet Name Service|Windows Internet Name Service. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|17|Vezeték nélküli helyi hálózat szolgáltatás|Vezeték nélküli LAN szolgáltatás. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|

Ha az előző tesztelési esetekkel kapcsolatos hibákkal találkozik, tekintse meg a megoldás táblázatának **Leírás** oszlopát. Ha további információra van szüksége, forduljon a támogatási csoporthoz. 

## <a name="data-disk-size-verification"></a>Adatlemez méretének ellenőrzése

Ha az adatlemezzel elküldött bármely kérelem mérete nagyobb, mint 1023 gigabájt (GB), a kérés nem lesz jóváhagyva. Ez a szabály a Linux és a Windows rendszerre egyaránt vonatkozik.

Küldje el újra a kérelmet a 1023 GB-nál kisebb vagy azzal egyenlő mérettel.

## <a name="os-disk-size-validation"></a>OPERÁCIÓSRENDSZER-lemez méretének ellenőrzése

Az operációsrendszer-lemez méretére vonatkozó korlátozásokkal kapcsolatban tekintse meg az alábbi szabályokat. Bármely kérelem elküldésekor ellenőrizze, hogy az operációsrendszer-lemez mérete a Linux vagy a Windows korlátozásán belül van-e.

|Operációs rendszer|Ajánlott VHD-méret|
|---|---|
|Linux|30 GB – 1023 GB|
|Windows|30 GB – 250 GB|

Mivel a virtuális gépek engedélyezik a hozzáférést az alapul szolgáló operációs rendszerhez, győződjön meg arról, hogy a VHD-méret elég nagy a VHD-hez. Mivel a lemezek állásidő nélkül nem bővíthetők, a lemez méretét 30 GB-ról 50 GB-ra kell használni.

|VHD-méret|Tényleges foglalt méret|Megoldás|
|---|---|---|
|>500 tebibájt (TiB)|n.a.|Kivétel jóváhagyásához forduljon a támogatási csoporthoz.|
|250-500 TiB|>200 gibibájtban értendők (GiB) eltérés a blob méretétől|Kivétel jóváhagyásához forduljon a támogatási csoporthoz.|

> [!NOTE]
> A nagyobb méretű lemezek nagyobb költségekkel járnak, és a telepítési és a replikálási folyamat során késésben lesznek. A késés és a költséghatékonyság miatt a támogatási csoport indoklást kérhet a kivétel jóváhagyásáról.

## <a name="wannacry-patch-verification-test-for-windows"></a>A Windows WannaCry javításának ellenőrzési tesztje

A WannaCry vírussal kapcsolatos potenciális támadás megelőzése érdekében győződjön meg arról, hogy az összes Windows-lemezképre vonatkozó kérelem frissítve lett a legújabb javítással.

Az operációs rendszer részletes verzióját a Windows Server javított verziójának és az általa támogatott minimális verziónak a következő táblázatban tekintheti meg: 

A képfájl verziója ellenőrizhető a `C:\windows\system32\drivers\srv.sys` vagy rendszerből `srv2.sys` .

> [!NOTE]
> A Windows Server 2019 nem rendelkezik a kötelező verzióra vonatkozó követelményekkel.

|Operációs rendszer|Verzió|
|---|---|
|A Windows a 2008 R2-t szolgálja|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>A SACK sebezhetőségi javításának ellenőrzése

Linux-rendszerképek beküldésekor a rendszer a kernel-verzióval kapcsolatos problémák miatt elutasítja a kérést.

Frissítse a kernelt jóváhagyott verzióval, majd küldje el újra a kérelmet. A jóváhagyott kernel-verziót az alábbi táblázatban találja. A verziószámnak az itt felsorolt számmal egyenlőnek vagy annál nagyobbnak kell lennie.

Ha a rendszerkép nincs telepítve a következő kernel-verziók egyikével, frissítse a megfelelő javításokkal. Kérje meg a szükséges jóváhagyást a támogatási csapattól a rendszerkép frissítése után a szükséges javításokkal:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|OPERÁCIÓSRENDSZER-család|Verzió|Kernel|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0 – 151| 
||14,04 LTS|4.15.0-1049-*-Azure|
||16,04 LTS|4.15.0 – 1049|
||18,04 LTS|4.18.0 – 1023|
||18,04 LTS|5.0.0 – 1025|
||18,10|4.18.0 – 1023|
||19,04|5.0.0-1010|
||19,04|5.3.0 – 1004|
|RHEL és cent operációs rendszer|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7,4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0 – 147|
||"7 – NYERS" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL – SAP 7,4|TBD|
||RHEL – SAP 7,5|TBD|
|SLES|SLES11SP4 (beleértve az SAP-t)|3.0.101-108.95.2|
||SLES12SP1 az SAP-hoz|3.12.74-60.64.115.1|
||SLES12SP2 az SAP-hoz|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 az SAP-hoz|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 az SAP-hoz|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 az SAP-hoz|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0 – 7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>A RHCK a fenti RHEL követi|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS stabil 2079.6.0|4.19.43*|
||Bétaverziós 2135.3.1|4.19.50*|
||Alpha 2163.2.1|4.19.50*|
|Debian|Jessie (biztonság)|3.16.68 – 2|
||Jessie backports|4.9.168 – 1 + deb9u3|
||stretch (biztonság)|4.9.168 – 1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, SID (stretch backports)|4.19.37 – 5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>A képméretnek megabájtnál többnek kell lennie

Az Azure-ban az összes virtuális merevlemeznek egy 1 megabájtos (MB) többszörös értékre igazított virtuális mérettel kell rendelkeznie. Ha a VHD-fájl nem felel meg az ajánlott virtuális méretnek, akkor előfordulhat, hogy a rendszer elutasítja a kérést.

A nyers lemezről a VHD-re történő átalakításkor kövesse az irányelveket, és győződjön meg arról, hogy a nyers lemez mérete több, mint 1 MB. További információ: [nem támogatott disztribúciók adatai](../../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Virtuális gép hozzáférése megtagadva

Ha a tesztelési esetek virtuális gépen való futtatása során a hozzáférés megtagadásával kapcsolatos problémák merülnek fel, előfordulhat, hogy nem rendelkezik megfelelő jogosultsággal a tesztelési esetek futtatásához.

Ellenőrizze, hogy engedélyezve van-e a megfelelő hozzáférés ahhoz a fiókhoz, amelyen az önteszt esetek futnak. Ha a hozzáférés nincs engedélyezve, engedélyezze a tesztelési esetek futtatását. Ha nem szeretné engedélyezni a hozzáférést, megoszthatja az egyéni tesztelési eset eredményeit a támogatási csapattal.

## <a name="download-failure"></a>Letöltési hiba
    
Tekintse meg az alábbi táblázatot a virtuálisgép-rendszerkép megosztott hozzáférési aláírás (SAS) URL-címével történő letöltésekor felmerülő problémákról.

|Használati eset|Hiba|Ok|Megoldás|
|---|---|---|---|
|1|A blob nem található|Lehet, hogy a VHD-t törölték vagy áthelyezték a megadott helyről.|| 
|2|BLOB használatban|A virtuális merevlemezt egy másik belső folyamat használja.|A VHD-nek a használatban lévő állapotban kell lennie, amikor letölti egy SAS URL-cím használatával.|
|3|Érvénytelen SAS URL-cím|A virtuális merevlemezhez tartozó SAS URL-cím helytelen.|Szerezze be a megfelelő SAS URL-címet.|
|4|Érvénytelen aláírás|A virtuális merevlemezhez tartozó SAS URL-cím helytelen.|Szerezze be a megfelelő SAS URL-címet.|
|6|HTTP feltételes fejléc|A SAS URL-címe érvénytelen.|Szerezze be a megfelelő SAS URL-címet.|
|7|Érvénytelen VHD-név|Ellenőrizze, hogy vannak-e speciális karakterek, például a százalék jele (%) vagy idézőjelek (") léteznek a VHD-névben.|Nevezze át a VHD-fájlt a speciális karakterek eltávolításával.|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>Első MB (2048 KB) partíció (csak Linux esetében)

A virtuális merevlemez elküldésekor győződjön meg arról, hogy a VHD első 2048 KB-a üres. Ellenkező esetben a kérés sikertelen lesz *.

>[!NOTE]
>* Bizonyos speciális rendszerképekhez, például az Azure Marketplace-ről az Azure-beli Windows alapképekre épülő, a számlázási címke megkeresése és a rendelkezésre álló belső értékekkel való megfelelés esetén figyelmen kívül hagyja a MB partíciót.

## <a name="default-credentials"></a>Alapértelmezett hitelesítő adatok

Mindig győződjön meg arról, hogy az alapértelmezett hitelesítő adatok nem lesznek elküldve a beküldött VHD-vel. Az alapértelmezett hitelesítő adatok hozzáadásával a VHD-k sebezhetővé teszik a biztonsági fenyegetéseket. Ehelyett hozzon létre saját hitelesítő adatokat a virtuális merevlemez elküldésekor.
  
## <a name="datadisk-mapped-incorrectly"></a>Helytelenül megfeleltetett adatlemez

Ha egy kérelem több adatlemezzel van elküldve, de a sorrendjük nem sorrendben van, akkor ez egy leképezési probléma. Ha például három adatlemez van, a számozási sorrendnek *0, 1, 2*értéknek kell lennie. Minden más rendelés leképezési problémaként van kezelve.

Küldje el újra a kérést az adatlemezek megfelelő előkészítésével.

## <a name="incorrect-os-mapping"></a>Helytelen operációs rendszer-hozzárendelés

Rendszerkép létrehozásakor előfordulhat, hogy a rendszer leképezi vagy hozzárendelte a helytelen operációsrendszer-címkét. Ha például az operációs rendszer neve részeként kiválasztja a **Windowst** a rendszerkép létrehozása közben, az operációsrendszer-lemezt csak Windows rendszerre kell telepíteni. Ugyanez a követelmény a Linux rendszeren is érvényes.

## <a name="vm-not-generalized"></a>Nem általánosított virtuális gép

Ha az Azure Marketplace-ről származó összes lemezképet újra fel kell használni, általánosítva kell lennie az operációs rendszer virtuális merevlemezének.

* **Linux rendszeren**a következő folyamat általánosítja a Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre.

  Az SSH ablakban adja meg a következő parancsot: `sudo waagent -deprovision+user`

* **Windows**esetén a használatával általánosíthatja a Windows-lemezképeket `sysreptool` .

További információ erről az eszközről: a [rendszer-előkészítés (Sysprep) áttekintése]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Adatlemez hibák

Az adatlemezzel kapcsolatos hibák megoldásához használja az alábbi táblázatot:

|Hiba|Ok|Megoldás|
|---|---|---|
|`DataDisk- InvalidUrl:`|Ez a hiba akkor fordulhat elő, ha az ajánlat elküldésekor érvénytelen szám van megadva a logikai egység számához (LUN).|Ellenőrizze, hogy az adatlemez LUN-számának sorszáma a partner Centerben van-e.|
|`DataDisk- NotFound:`|Ez a hiba akkor fordulhat elő, ha egy adatlemez nem található a megadott SAS URL-címen.|Ellenőrizze, hogy az adatlemez a kérelemben megadott SAS URL-címen található-e.|

## <a name="remote-access-issue"></a>Távelérési probléma

Ha a Windows-rendszerképhez nincs engedélyezve a RDP protokoll (RDP) beállítás, akkor ez a hibaüzenet jelenik meg. 

Az RDP-hozzáférés engedélyezése a Windows-rendszerképekhez a beküldése előtt.

## <a name="bash-history-failed"></a>A bash előzményei nem sikerültek

Ez a hiba akkor jelenik meg, ha az elküldött rendszerképben lévő bash-előzmények mérete meghaladja az 1 kilobájtot (KB). A méret 1 KB-ra van korlátozva, így biztosítható, hogy a rendszer ne rögzítse az esetlegesen bizalmas adatokat a bash-előzmények fájljában.

Alább láthatók a "bash-előzmények" törlésének lépései.

1. lépés Telepítse a virtuális gépet, és kattintson a "parancs futtatása" lehetőségre Azure Portal.
![Parancs futtatása Azure Portal](./media/vm-certification-issues-solutions-3.png)

2. lépés Válassza az első "RunShellScript" lehetőséget, és futtassa az alábbi parancsot.

Parancs: "Cat/dev/null eszközre > ~/. bash_history && History-c" ![ bash History parancs on Azure Portal](./media/vm-certification-issues-solutions-4.png)

3. lépés A parancs sikeres végrehajtása után indítsa újra a virtuális gépet.

4. lépés: Általánosítsa a virtuális gépet, végezze el a rendszerkép VHD-jét, és állítsa le a virtuális gépet.

5. lépés     Küldje el újra az általánosított képet.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Kivételek (egyéni sablonok) kérelmezése a virtuálisgép-rendszerképeken szelektív tesztek esetén

A kiadók a virtuális gépek minősítése során végrehajtott tesztek alól kivételeket is kérhetnek. A kivételeket rendkívül ritka esetekben kell megadnia, amikor a kiadó bizonyítékot szolgáltat a kérés támogatásához.
A minősítési csapat fenntartja a jogot arra, hogy bármikor megtagadja vagy jóváhagyja a kivételeket.

Az alábbi részekben olyan főbb forgatókönyvekkel fogunk foglalkozni, amikor a rendszer kivételeket kér, és hogyan kérhet kivételt.

Kivételek forgatókönyvei

Három forgatókönyv/eset van, ahol a kiadók általában a kivételeket kérik. 

* **Kivétel egy vagy több tesztelési eset esetében:** A kiadók a [Piactéri támogatási](https://aka.ms/marketplacepublishersupport) kérelmekre vonatkozó kivételeket is elérnek a tesztelési esetekhez. 

* **Zárolt virtuális gépek/nincs gyökérszintű hozzáférés:** Néhány közzétevőnek van olyan forgatókönyve, amelyben a virtuális gépeket zárolni kell, mert olyan szoftverekkel rendelkeznek, mint például a virtuális gépre telepített tűzfalak. 
       Ebben az esetben a kiadók itt tölthetik le a [Certified test Tool eszközt](https://aka.ms/AzureCertificationTestTool) , és megadhatják a jelentést a [Marketplace Publisher támogatási szolgálatában](https://aka.ms/marketplacepublishersupport)


* **Egyéni sablonok:** Egyes közzétevők olyan virtuálisgép-rendszerképeket tesznek közzé, amelyekhez egyéni ARM-sablon szükséges a virtuális gépek telepítéséhez. Ebben az esetben a kiadóknak meg kell adniuk az egyéni sablonokat a [piactér-közzétevő támogatásában](https://aka.ms/marketplacepublishersupport) , így a minősítési csapat is használhatja az ellenőrzést. 

### <a name="information-to-provide-for-exception-scenarios"></a>Kivételi forgatókönyvek megadására szolgáló információk

A kiadóknak el kell érniük a támogatási szolgálatot a [Marketplace kiadó támogatásában](https://aka.ms/marketplacepublishersupport) a fenti forgatókönyvhöz tartozó kivételek kérelmezéséhez a további következő információkkal:

   1.   Közzétevő azonosítója – közzétevő azonosítója a partner Center portálon
   2.   Ajánlat azonosítója/neve – az ajánlat azonosítója/neve, amelyre a kivételt kérték 
   3.   SKU/csomag azonosítója – a virtuálisgép-ajánlathoz tartozó csomag azonosítója/SKU, amelyre a kivételt kérték
   4.    Verzió – annak a virtuálisgép-ajánlatnak a verziója, amelyre a kivételt kérték
   5.   Kivétel típusa – tesztek, zárolt virtuális gép, egyéni sablonok
   6.   Kérelem oka – a kivétel oka, valamint az arra vonatkozó információk, hogy milyen teszteket kell kizárni 
   7. Ütemterv – a kivétel kérésének dátuma 
   8.   Melléklet – bármilyen fontos dokumentum csatolása. A zárolt virtuális gépek esetében csatolja a teszt jelentést, és az egyéni sablonokhoz adja meg az egyéni ARM-sablont mellékletként. Nem sikerült csatolni a jelentést a zárolt virtuális gépekhez és az egyéni ARM-sablonhoz az egyéni sablonok esetén a rendszer megtagadást eredményez.


## <a name="next-steps"></a>Következő lépések

Ha kérdése vagy visszajelzése van a fejlesztéssel kapcsolatban, forduljon a [partner Center ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).
