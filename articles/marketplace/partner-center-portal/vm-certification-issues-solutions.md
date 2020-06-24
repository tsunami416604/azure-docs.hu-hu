---
title: A virtuális gép hitelesítése – problémák és megoldások
description: Ez a cikk a virtuálisgép-lemezképekkel kapcsolatos gyakori hibaüzeneteket ismerteti. Emellett a kapcsolódó megoldásokat is tárgyalja
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: dcf687a369b32b2055f579f2599a0c3097f9f9f3
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977707"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>A virtuális gép hitelesítése – problémák és megoldások

A virtuális gép (VM) lemezképének az Azure Marketplace-en való közzétételekor az Azure-csapat ellenőrzi a virtuálisgép-lemezképet, hogy biztosítsa a rendszerindítást, a biztonságot és az Azure-kompatibilitást. Ha a kiváló minőségű tesztek bármelyike meghiúsul, a közzététel sikertelen lesz, és a hibát tartalmazó üzenet jelenik meg.

Ez a cikk a virtuálisgép-lemezképekkel kapcsolatos gyakori hibaüzeneteket ismerteti. Emellett a kapcsolódó megoldásokat is tárgyalja:

> [!NOTE]
> Ha kérdése vagy visszajelzése van a fejlesztéssel kapcsolatban, forduljon a [partner Center ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Jóváhagyott alaprendszerkép

Ha olyan kérést küld, amely a rendszerkép frissítéssel való újbóli közzétételét kéri, akkor előfordulhat, hogy a részleges ellenőrzés tesztelési esete meghiúsul. Ebben az esetben a rendszerkép nem lesz jóváhagyva.

Ez a hiba akkor fordul elő, ha egy másik közzétevőhöz tartozó alaprendszerképet használ, és frissítette a rendszerképet. Ebben az esetben a rendszerkép közzététele nem engedélyezett.

A probléma megoldásához kérje le legújabb rendszerképét az Azure Marketplace-ről, és végezze el a lemezkép módosítását. A következő témakörben megtekintheti a jóváhagyott alapképeket, ahol megkeresheti a lemezképet:

- [Linux – rendszerképek](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json)
- [Windows – rendszerképek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd#select-an-approved-base)

## <a name="vm-extension-failure"></a>VM-bővítmény hibája

Az alábbi lépéseket követve ellenőrizheti, hogy a rendszerkép támogatja-e a virtuálisgép-bővítményt:

Virtuálisgép-bővítmények engedélyezése:

1. Válassza ki a Linux rendszerű virtuális gépet.
2. Lépjen a **diagnosztikai beállítások menüpontra**.
3. Az alapmátrixok engedélyezéséhez frissítse a **Storage-fiókot**.
4. Kattintson a **Mentés** gombra.

   ![Vendégszintű monitorozás engedélyezése](./media/vm-certification-issues-solutions-1.png)

Győződjön meg arról, hogy a virtuálisgép-bővítmények megfelelően vannak aktiválva:

5. Nyissa meg a virtuális gép virtuálisgép- **bővítmények** lapját, és ellenőrizze a **Linux diagnosztikai bővítményt**.
6. Ha az állapot kiosztása **sikeres** volt, akkor a bővítmények tesztelési esete lett átadva.
7. Ha az állapot kiosztása **nem sikerült** , akkor a bővítmények tesztelési esete meghiúsult, és be kell állítania a megerősített jelzőt.

   ![Sikeres kiépítés](./media/vm-certification-issues-solutions-2.png)

   Ha a virtuálisgép-bővítmény nem sikerül, lépjen a [Linux diagnosztikai bővítmény használata elemre a metrikák és naplók figyeléséhez](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux) az engedélyezéshez. Ha nem szeretné, hogy a virtuálisgép-bővítmény engedélyezve legyen, forduljon a támogatási csapathoz, és kérje meg, hogy tiltsa le a bővítményt.

## <a name="virtual-machine-provisioning-issue"></a>Virtuális gépek kiépítési hibája

Az ajánlat elküldése előtt győződjön meg arról, hogy a kiépítési folyamat szigorúan követi a virtuális gépet. A virtuális gép kiépítési JSON-formátumának megtekintéséhez nyissa meg az [Azure virtuális gép (VM) rendszerképének minősítését](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template).

A kiépítési problémák a következő meghibásodási helyzetekben lehetnek:

|S.NO|error|reason|megoldás|
|---|---|---|---|
|1|Érvénytelen virtuális merevlemez (VHD)|Ha a VHD-láblécben megadott cookie-érték nem megfelelő, akkor a VHD-fájl érvénytelennek tekintendő.|Hozza létre újból a rendszerképet, és küldje el a kérést.|
|2|Érvénytelen blob-típus|A virtuális gép kiépítés meghiúsult, mert a használt blokk egy lap típusa helyett blob típusú.|Hozza létre újból a rendszerképet, és küldje el a kérést.|
|3|Kiépítési időtúllépés vagy nem megfelelően általánosítva|Probléma van a virtuális gépek általánosításával.|Hozza létre újra a rendszerképet az általánosítással, majd küldje el a kérelmet.|

> [!NOTE]
> A virtuális gépek általánosításával kapcsolatos dokumentációhoz kövesse az alábbi hivatkozásokat:
> - [Linux](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-configure-vm#generalize-the-image)
> - [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Windows rendszerhez készült szoftverek megfelelősége

Ha a Windows-rendszerkép iránti kérelmet a szoftver megfelelősége miatt utasították el, akkor lehetséges, hogy létrehozott egy Windows-rendszerképet, amelyen telepítve van az SQL Server, ahelyett, hogy az Azure Marketplace-ről a megfelelő SQL-verziót kellene használnia.

Ne hozzon létre saját Windows-rendszerképet, amelyben az SQL Server telepítve van. Ehelyett használja a jóváhagyott SQL alaplemezképeket (Enterprise/standard/web) az Azure Marketplace-ről.

Ha a Visual studiót vagy bármely Office licenccel rendelkező terméket próbál telepíteni, az előzetes jóváhagyáshoz forduljon a támogatási csoporthoz.

További információ: Azure-beli [virtuális gépek technikai eszközeinek létrehozása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd#select-an-approved-base) egy jóváhagyott alap kiválasztásához.

## <a name="tool-kit-test-case-execution-failed"></a>Az eszközkészlet tesztelési esetének végrehajtása nem sikerült

A Microsoft minősítési eszközkészlet segítséget nyújt a tesztelési esetek végrehajtásában annak ellenőrzéséhez, hogy a VHD/rendszerkép kompatibilis-e az Azure-környezettel.

Töltse le a [Microsoft minősítési eszközkészletet](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

## <a name="linux-test-cases"></a>Linux-tesztelési esetek

A következő Linux-tesztelési eseteket kell végrehajtani az eszközkészlet végrehajtásához. A teszt érvényesítése a leírásban van megadva.

|Sorszám|tesztelési esetek|leírás|
|---|---|---|
|1|Bash-előzmények|A rendszer a virtuális gép rendszerképének létrehozása előtt törli a bash-előzmények fájljait.|
|2|Linux-ügynök verziója|Az Azure Linux Agent 2.2.41 és újabb rendszereken is telepítve kell lennie.|
|3|Szükséges kernel-paraméterek|A következő kernel-paraméterek ellenőrzése: <br>konzol = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Partíció cseréje az operációsrendszer-lemezen|Ellenőrzi, hogy a swap partíciók nincsenek-e létrehozva az operációsrendszer-lemezen.|
|5|Rendszerlemez gyökérkönyvtára|Hozzon létre egy gyökérszintű partíciót az operációsrendszer-lemez számára.|
|6|OpenSSL-verzió|Az OpenSSL verziójának nagyobbnak vagy egyenlőnek kell lennie a v 0.9.8.|
|7|Python-verzió|A Python 2.6 + verziója kifejezetten ajánlott.|
|8|Ügyfél élettartamának intervalluma|ClientAliveInterval beállítása 180-re. Az alkalmazásra vonatkozó igény esetén 30 – 235 közötti érték adható meg. Ha engedélyezi az SSH-t a végfelhasználók számára, ezt az értéket a magyarázatnak megfelelően kell beállítani.|
|9|Operációs rendszer architektúrája|Kizárólag a 64 bites operációs rendszerek támogatottak.|
|10|Automatikus frissítés|Meghatározza, hogy engedélyezve van-e a Linux-ügynök automatikus frissítése.|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>Gyakori hibák találhatók az előző tesztelési esetek végrehajtásakor

Gyakori hibák találhatók az előző tesztelési esetek végrehajtása során.
 
|S.NO|tesztelési eset|error|megoldás|
|---|---|---|---|
|1|Linux-ügynök verziója – tesztelési eset|A Linux-ügynök minimális verziója 2,241 vagy újabb. Ezt a követelményt a 2020. május 1. óta kötelező megadni.|A [kérés elküldéséhez](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)a rendszerképet a szükséges verzióval kell frissíteni.|
|2|Bash-előzmények tesztelési esete|Hibaüzenet jelenik meg, ha az elküldött képen a bash-előzmények mérete meghaladja az 1 KB-ot. A méret 1 KB-ra van korlátozva, így biztosítható, hogy a rendszer ne rögzítse az esetlegesen bizalmas adatokat a bash-előzmények fájljában.|A probléma megoldásához csatlakoztassa a virtuális merevlemezt bármely más működő virtuális géphez, és végezze el a kívánt módosításokat (például törölje az `.bash` előzményeket), hogy a méretet 1 KB-nál kisebb vagy azzal egyenlőre csökkentse.|
|3|Szükséges kernel-paraméterek tesztelési esete|Ez a hibaüzenet akkor jelenik meg, ha a **konzol** értéke nem **ttyS0**értékre van állítva. Ellenőrzés a parancs végrehajtásával:<br>`cat /proc/cmdline`|Állítsa be a **konzol** értékét a **ttyS0** , majd küldje el újra a kérelmet.|
|4|ClientAlive intervalluma – tesztelési eset|Ha az eszközkészlet eredménye sikertelen eredményt ad a tesztelési esethez, a **ClientAliveInterval**nem megfelelő értékkel rendelkezik.|Állítsa a **ClientAliveInterval** értéket a 235 értéknél kisebbre vagy azzal egyenlőre, majd küldje el újra a kérelmet.|

### <a name="windows-test-cases"></a>Windows-tesztelési esetek

Az eszközkészlet az alábbi Windows-tesztelési eseteket fogja végrehajtani. A teszt érvényesítése a leírásban van megadva.

|Sorszám|tesztelési esetek|leírás|
|---|---|---|---|
|1|Operációs rendszer architektúrája|Az Azure csak a 64 bites operációs rendszereket támogatja.|
|2|Felhasználói fióktól való függőség|Az alkalmazás végrehajtásának nem kell a rendszergazdai fióktól függőnek lennie.|
|3|Feladatátvevő fürt|A Windows Server feladatátvételi fürtszolgáltatás még nem támogatott. az alkalmazás nem függ ettől a szolgáltatástól.|
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
|15|SNMP-szolgáltatások|Az SNMP-szolgáltatások szolgáltatás még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|16|Windows Internet Name Service|Windows Internet Name Service. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|17|Vezeték nélküli helyi hálózat szolgáltatás|Vezeték nélküli LAN szolgáltatás. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|

Ha a fenti tesztelési esetekkel kapcsolatos hibákat tapasztal, tekintse meg a megoldás előző táblázatának **Leírás** oszlopát. Ha további információra van szüksége, forduljon a támogatási csoporthoz. 

## <a name="data-disk-size-verification"></a>Adatlemez méretének ellenőrzése

Ha az adatlemezzel elküldött kérések mérete meghaladja a 1023 GB-ot, a kérés nem lesz jóváhagyva. Ez a szabály a Linux & Windowsra is vonatkozik.

küldje el újra a kérelmet a 1023 GB-nál kisebb vagy azzal egyenlő mérettel.

## <a name="os-disk-size-validation"></a>OPERÁCIÓSRENDSZER-lemez méretének ellenőrzése

Az operációsrendszer-lemez méretére vonatkozó korlátozásokkal kapcsolatban tekintse meg az alábbi szabályokat. Bármely kérelem elküldésekor ellenőrizze, hogy az operációsrendszer-lemez mérete a Linux vagy a Windows korlátozásán belül van-e.

|Operációs rendszer|ajánlott VHD-méret|
|---|---|
|Linux|30 GB – 1023 GB|
|Windows|30 GB – 250 GB|

Mivel a virtuális gépek engedélyezik a hozzáférést az alapul szolgáló operációs rendszerhez, győződjön meg arról, hogy a VHD-méret elég nagy a VHD-hez. Mivel a lemezek állásidő nélkül nem bővíthetők, a lemez mérete 30 és 50 GB között kell, hogy legyen.

|VHD-méret|tényleges foglalt méret|megoldás|
|---|---|---|
|>500 TiB|n.a.|kivétel jóváhagyásához forduljon a támogatási csoporthoz.|
|250-500 TiB|>200 GiB eltér a blob méretétől|kivétel jóváhagyásához forduljon a támogatási csoporthoz.|

> [!NOTE]
> A nagyobb méretű lemezek nagyobb költségekkel járnak, és a létesítési és a replikálási lépések során késésben lesznek. A késés és a költséghatékonyság miatt a támogatási csoport indoklást kérhet a kivétel jóváhagyásáról.

## <a name="wannacry-patch-verification-test-for-windows"></a>A Windows WannaCry javításának ellenőrzési tesztje

A WannaCry vírussal kapcsolatos potenciális támadás megelőzése érdekében győződjön meg arról, hogy az összes Windows-lemezképre vonatkozó kérelem frissítve lett a legújabb javítással.

A Windows Server javított verziójának vizsgálatához tekintse meg az operációs rendszer részletes ismertetését és az általa támogatott minimális verziót a következő táblázatban. 

A képfájl verziója ellenőrizhető a `C:\windows\system32\drivers\srv.sys` vagy rendszerből `srv2.sys` .

> [!NOTE]
> A WindowsServer2019 nem rendelkezik a kötelező verzióra vonatkozó követelményekkel.

|Operációs rendszer|version|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>A SACK sebezhetőségi javításának ellenőrzése

Linux-rendszerképek beküldésekor a rendszer elutasítja a kérést, mert a kernel verziója probléma merült fel.

Frissítse a kernelt egy jóváhagyott verzióval, és küldje el újra a kérelmet. A jóváhagyott kernel-verziót az alábbi táblázatban találja. A verziószámnak egyenlőnek vagy annál nagyobbnak kell lennie az alább felsoroltak közül.

Ha a lemezkép nem a következő kernel-verziók egyikével lett telepítve, frissítse a lemezképet a megfelelő javításokkal. További információt az alábbi hivatkozásokon talál. Kérje meg a szükséges jóváhagyást a támogatási csapattól a rendszerkép frissítése után a szükséges javításokkal:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|OPERÁCIÓSRENDSZER-család|version|rendszermag|
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
||7.4|3.10.0-693.50.3|
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

Az Azure-ban az összes virtuális merevlemeznek legalább 1 MB-nak kell lennie. Ha a VHD-fájl nem felel meg az ajánlott virtuális méretnek, akkor előfordulhat, hogy a rendszer elutasítja a kérést.

kövesse az irányelveket, ha nyers lemezről VHD-re konvertál, és biztosítania kell, hogy a nyers lemez mérete több, mint 1 MB. További információ: [nem támogatott disztribúciók információi](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)

## <a name="vm-access-denied"></a>Virtuális gép hozzáférése megtagadva

Ha a virtuális gépen a tesztelési esetek végrehajtása során megtagadta a hozzáférés-megtagadási problémákat, annak oka az lehet, hogy nincs megfelelő jogosultsága a tesztelési esetek végrehajtásához.

Ellenőrizze, hogy engedélyezve van-e a megfelelő hozzáférés ahhoz a fiókhoz, amelyen az önteszt eseteket végrehajtja. Ha nem, engedélyezze a hozzáférést a tesztelési esetek végrehajtásához. Ha nem szeretné engedélyezni a hozzáférést, megoszthatja az egyéni tesztelési eset eredményeit a támogatási csapattal.

## <a name="download-failure"></a>Letöltési hiba
    
A virtuálisgép-rendszerkép SAS URL-lel történő letöltésekor a következő táblázatban talál további információt.

|S.NO|error|reason|megoldás|
|---|---|---|---|
|1|A blob nem található|Lehet, hogy a VHD-t törölték vagy áthelyezték a megadott helyről.|| 
|2|BLOB használatban|A virtuális merevlemezt egy másik belső folyamat használja|A virtuális merevlemeznek a SAS URL-cím használatával történő letöltéskor használt állapotban kell lennie.|
|3|Érvénytelen SAS URL-cím|A virtuális merevlemezhez tartozó SAS URL-cím helytelen.|Szerezze be a megfelelő SAS URL-címet.|
|4|Érvénytelen aláírás|A virtuális merevlemezhez tartozó SAS URL-cím helytelen.|Szerezze be a megfelelő SAS URL-címet.|
|6|HTTP feltételes fejléc|Érvénytelen SAS URL-cím.|Szerezze be a megfelelő SAS URL-címet.|
|7|Érvénytelen VHD-név|Győződjön meg arról, hogy **%** a VHD-névben vannak-e olyan speciális karakterek, mint a vagy a **""**|A VHD-fájl átnevezése speciális karakterek eltávolításával|

## <a name="first-1-mb-partition"></a>Első 1 MB-os partíció

A virtuális merevlemez elküldésekor győződjön meg arról, hogy a VHD első 1 MB-os partíciója üres. Ellenkező esetben a kérés sikertelen lesz.

## <a name="default-credentials"></a>Alapértelmezett hitelesítő adatok

Mindig győződjön meg arról, hogy az alapértelmezett hitelesítő adatok nem lesznek elküldve a beküldött VHD-vel. Az alapértelmezett hitelesítő adatok hozzáadásával a VHD-k sebezhetővé teszik a biztonsági fenyegetéseket. Ehelyett hozzon létre saját hitelesítő adatokat a virtuális merevlemez elküldésekor.
  
## <a name="datadisk-mapped-incorrectly"></a>Helytelenül megfeleltetett adatlemez

Ha egy kérelem több adatlemezzel van elküldve, de a sorrendjük nem sorrendben van, akkor ez egy leképezési probléma. Ha például három adatlemez van, a számozási sorrendnek **0, 1, 2**értéknek kell lennie. A rendszer minden más rendelést leképezési problémaként kezel.

küldje el újra a kérést az adatlemezek megfelelő előkészítésével.

## <a name="incorrect-os-mapping"></a>Helytelen operációs rendszer-hozzárendelés

Rendszerkép létrehozásakor előfordulhat, hogy a rendszer leképezi vagy hozzárendeli a helytelen operációsrendszer-címkéhez. Ha például a **Windows** az operációs rendszer neve részeként van kiválasztva a rendszerkép létrehozásakor, az operációsrendszer-lemezt csak Windows rendszerrel kell telepíteni. Ugyanez vonatkozik a Linuxra is.

## <a name="vm-not-generalized"></a>Nem általánosított virtuális gép

Ha az Azure Marketplace-ről származó összes lemezképet újra fel kell használni, az operációs rendszer VHD-jét általánosítani kell.

Linux:

A következő folyamat általánosít egy Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre.

Az SSH ablakban adja meg a következő parancsot:`sudo waagent -deprovision+user`

Windows:

A Windows-lemezképek általánosítva vannak a-vel `sysreptool` .

Az eszközről a [Sysprep (rendszer-előkészítés) áttekintése című]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) témakörben talál további információt.

## <a name="datadisk-error"></a>Adatlemez hiba

Az adatlemezzel kapcsolatos hibákat a következő táblázat tartalmazza.

|Hiba|reason|megoldás|
|---|---|---|
|`DataDisk- InvalidUrl:`|A hiba oka az lehet, hogy érvénytelen szám van megadva a LUN számára az ajánlat elküldésekor.|Ellenőrizze, hogy az adatlemez LUN-számának sorszáma a partner Centerben van-e.|
|`DataDisk- NotFound:`|A hiba oka az lehet, hogy a megadott SAS URL-címen nem található adatlemez|Ellenőrizze, hogy az adatlemez a kérelemben megadott SAS URL-címen található-e.|

## <a name="remote-access-issue"></a>Távelérési probléma

Ha az RDP-beállítás nincs engedélyezve a Windows-rendszerkép esetében, akkor ez a hibaüzenet jelenik meg. 

Az elküldés előtt engedélyezze a Windows-lemezképek RDP-hozzáférését.

## <a name="next-steps"></a>Következő lépések

Ha kérdése vagy visszajelzése van a fejlesztéssel kapcsolatban, forduljon a [partner Center ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).
