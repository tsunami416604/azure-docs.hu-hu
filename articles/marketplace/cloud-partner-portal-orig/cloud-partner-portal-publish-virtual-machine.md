---
title: Szerző virtuálisgép-ajánlat |} A Microsoft Docs
description: Virtuális gép közzététele az Azure piactéren.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 3b046022990e95e65ed02880bd3fefbd78bcad28
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387360"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Virtuális gép közzététele az Azure piactéren

A cikk ismerteti a lépéseket kell egy virtuálisgép-ajánlat közzététele az Azure piactéren.

## <a name="prerequisites"></a>Előfeltételek

A következő műszaki és nem technikai jellegű Előfeltételek vonatkoznak közzététele egy virtuális gépet az Azure Marketplace-en

### <a name="technical"></a>Műszaki

-   [Virtuálisgép-lemezkép létrehozása az Azure Marketplace a műszaki előfeltételei](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Egy Linux VHD létrehozása és feltöltése](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [& Tesztelje egy rendszerképből egy Linux rendszerű virtuális gép létrehozása](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [A Windows VHD létrehozása és feltöltése ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [& Tesztelje egy rendszerképből egy Windows virtuális gép létrehozása](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Virtuális merevlemez létrehozása során tapasztalt gyakori hibáinak elhárítása](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Biztonsági javaslatok az Azure Marketplace-rendszerképek](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>Nem technikai jellegű (üzleti követelmények)

 -   A vállalatnak (vagy az leányvállalatának) az Azure piactér által támogatott vevői országban található

-   A termék licenccel kell rendelkezniük, amely kompatibilis a számlázási modellek az Azure piactér által támogatott módon

-   Ön felelős az ügyfelek számára elérhetővé tétele az műszaki támogatást, olyan üzletileg ésszerű módon. Ez a támogatás lehet ingyenes, fizetős vagy közösségi.

-   Ön felelős a szoftver- és külső függőségek licencelése.

-   Azt adja meg a tartalmat, amely megfelel a feltételeknek az Azure Marketplace-en és az Azure felügyeleti portálon való megjelentetéséhez ajánlatban.

-   Ön vállalja, hogy az Azure piactér részvételi szabályzatának és a kiadói szerződés feltételeit.

-   Ahhoz, hogy elfogadja a [használati](https://azure.microsoft.com/support/legal/website-terms-of-use/) , [Microsoft adatvédelmi nyilatkozatát](http://www.microsoft.com/privacystatement/default.aspx), és [a Microsoft Azure Certified Program szerződését](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="before-you-begin"></a>Előkészületek

Minden előfeltétel többszörösen elindíthatja a Megoldássablon ajánlat készítése. Mielőtt elkezdené, tekintse át a következő ajánlat és a Termékváltozat-információk.

**Ajánlat**

Az Azure-alkalmazás ajánlat termék, ajánlat való kiadói osztály felel meg. Ha egy új típusú megoldás/alkalmazás, amely érhető el az Azure Marketplace-en szeretne, egy új ajánlatunk, a legjobb módszer. Az ajánlatok SKU-k gyűjteményei. Minden ajánlat az Azure Marketplace-en a saját entitás jelenik meg.

**Termékváltozat**

Az SKU egy ajánlat legkisebb megvásárolható egysége. Az azonos termék osztály (ajánlat) belül SKU-k lehetővé teszik megkülönböztetni a különböző támogatott funkciókkal. Ha például az ajánlat kezeli, vagy nem felügyelt, és eltérő számlázási modellek támogatottak.

Több SKU-k hozzáadása a következő esetekben:
- Különböző számlázási modell, például a Bring Your saját licences (BYOL) vagy a Használatalapú fizetés (Használatalapú) támogatni kíván.
- Minden egyes Termékváltozat támogatja a különböző funkciókészlettel, és minden egyes szolgáltatáskészlet ezekhez különböző árak.

A Termékváltozat a szülő ajánlat az Azure Marketplace-en alatt jelenik meg, és megjelenik-e az Azure Portalon a saját megvásárolható entitásként.

## <a name="to-create-a-new-offer"></a>Az új ajánlat létrehozása

1.  Jelentkezzen be a [Cloud partner Portalon](http://cloudpartner.azure.com/).

2.  A bal oldali navigációs sávon válassza **+ új ajánlat**, majd válassza ki **virtuális gépek**.

    ![Új ajánlat azon virtuális gépek](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  A **új ajánlat**válassza **szerkesztő**.

![Új ajánlat szerkesztő](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  A **szerkesztő**, ehhez meg kell adnia a következő nézetekben található információk:
    - Ajánlat beállításai
    - Termékváltozatok
    - Piactér
    - Támogatás minden egyes nézet tartalmazza a kérik a mezők halmaza alapján. Szükséges, piros csillag jelöl, mezők (\*)

## <a name="to-configure-offer-settings"></a>Az ajánlat beállításainak konfigurálása

1. Adja meg az alábbiakat **ajánlat identitás** ajánlat beállítások mezőket.

    **Ajánlat azonosítója**

     Közzétevő-profilon belül az ajánlat egyedi azonosítója. Ez az azonosító látható termék URL-címeket, az Azure Resource Manager-sablonok, és számlázási jelenti. Csak kisbetűs alfanumerikus karaktereket és kötőjeleket (-) használhatja. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakter hosszú lehet. Például, ha a kiadó **contoso** közzéteszi az ajánlatot Azonosítóval rendelkező ajánlat **minta-vm**, akkor jelenik meg, mint az Azure Marketplace-en **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
    >[!Note]
    >Ez a mező zárolva van, amikor egy ajánlatot élesíti.

    **Közzétevő azonosítója**

    Egy legördülő lista közzétevő profil. Válassza ki, amely alatt az ajánlatot közzétenni kívánt profilt. 
    >[!Note]
    >Ez a mező zárolva van, amikor egy ajánlatot élesíti.

    **Name (Név)**

    Az ajánlat megjelenített neve. Ez a név jelenik meg az Azure Marketplace-en és az Azure Portalon. Legfeljebb 50 karakterből állhat. Használja az alábbi útmutatót az ajánlat neve:
    -  A termék számára egy felismerhető márkanevet adjon meg. 
    - Itt a vállalat neve ne tartalmazza, kivéve, ha ez az ajánlat hogyan kereskedelmi forgalomba.
    - Ha ez az ajánlat a saját webhelyen található már termékmarketinggel, győződjön meg arról, a név legyen azonos a neve, a webhelyen.

2. Válassza ki **mentése** ajánlat beállítások befejezéséhez.

## <a name="to-create-a-sku"></a>A Termékváltozat létrehozása

1. Válassza ki **termékváltozatok**. 
2. Válassza ki **adjon hozzá egy Termékváltozatot** , adjon meg egy Termékváltozatot. Az SKU-azonosítója az ajánlat belül termékváltozat egyedi azonosító. Ez az azonosító látható termék URL-címeket, az Azure Resource Manager-sablonok, és számlázási jelenti. A Termékváltozat azonosítója:
    - Legfeljebb egy legfeljebb 50 karakter hosszú lehet.
    - Csak lehet a kisbetűs alfanumerikus karaktereket és kötőjeleket (-).
    - Az azonosító nem végződhet kötőjellel.

    ![Adjon hozzá egy Termékváltozatot](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>Termékváltozatának adatait konfigurálása

Miután hozzáadta a Termékváltozat, az SKU-k megtekintése a termékváltozatok listája jelenik meg. A Termékváltozat részletek megtekintéséhez válassza ki a Termékváltozat nevét. A részletes nézete segítségével adja meg információkat az alábbi mezőkben.

### <a name="hide-this-sku"></a>Ez a Termékváltozat elrejtése

Ez a beállítás segítségével kezelheti a Termékváltozat látható-e. "Ez Termékváltozat elrejtése" be van kapcsolva, a Termékváltozat-e látható [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és a [az Azure Portal](https://portal.azure.com/) az ügyfelek számára. Előfordulhat, hogy el szeretné rejteni a Termékváltozat, ha csak szeretné elérhető megoldássablonok keresztül, és nem a vásárlás külön-külön.

### <a name="cloud-availability"></a>Felhőalapú rendelkezésre állása

Ez a mező lehetővé teszi, hogy a rendelkezésre állási termékváltozat definiálja a különböző Azure-felhő.

**Nyilvános Azure**

Ez a Termékváltozat is üzembe helyezhetők az ügyfelek számára minden nyilvános Azure-régióban, amely rendelkezik a Marketplace-integráció.

**Az Azure Government Cloud**

Ez a Termékváltozat a Azure Government felhőben is telepíthető. Közzététel előtt [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), azt javasoljuk, hogy a kiadók és a megoldás együttműködik hitelesíti a várt módon. Szakasz és vizsgálat [kérése egy próbafiókot](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>A Microsoft Azure Government egy kormányzatok számára készült közösségi felhő ügyfeleknek az USA szövetségi, állapot, helyi vagy törzsi közösségi, és partnerek jogosult ezeket az entitásokat kiszolgálása érdekében szabályozott hozzáféréssel rendelkező.

### <a name="countryregion-availability"></a>Elérhetőség ország/régió

Ez a mező azonosítja a régió, amelyben a Termékváltozat fog elérhető és megvásárolható kiadása. Alaposan gondolja át, ahol elérhetővé a termékváltozatok kell. Egyes országok besorolt "Microsoft adó elengedik ország".

-   A "Microsoft adó elengedik ország" a Microsoft adók gyűjt az ügyfelektől származó, és fizet a kormányzati (kap) adók.

-   Más országokban partnerek felelős adók ügyfelek gyűjtése és az adók és a kormány. Ha ezekben az országokban, hogy választja, a funkció alapján számítja ki, és azokat az adókat fizet kell rendelkeznie.

![Elérhetőség ország/régió kiválasztása](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>Díjszabás

Jelenleg két díjszabási modellek támogatottak.

#### <a name="bring-your-own-license-byol"></a>Bring-Your-saját licenc (használata BYOL)

Kezelheti a virtuális gépen futó szoftver licencelése. A Microsoft csak díja szerint számítjuk fel az infrastrukturális költségeket.

#### <a name="usage-based-monthly-billed-sku"></a>Használat alapján, havi díját Termékváltozat

Ügyfelek által a kiadók a Virtuálisgép-méretek a díjszabási táblázat alapján óránkénti alapon kell fizetnie. Az **óradíjas** modell a termékváltozatok teljes költsége a szoftver költségét a közzétevő alapján számítjuk fel, a infrastruktúra díjat számítunk fel a Microsoft által a háttéradatok lesz. A teljes költség jelenik meg az ügyfél egy óránkénti és havi díja, a vásárlás kiválasztásakor. A számlázás ebben az esetben lesz havonta.

A Használatalapú modell belül szükség további beállítások tartoznak.

**Ingyenes próbaverzió**

Megadhatja, ha lehetővé szeretné tenni az ügyfelek számára egy ingyenes próbaverzióra.
Itt az ügyfélnek nem kell fizetnie szoftverköltség az első 30 vagy 90 nap során (a kijelölt függően) a virtuális gép üzembe helyezése után. Az ingyenes próbaidőszak eltelte után akkor kell fizetnie az óradíjas modellben a kiadók által meghatározott díjszabás alapján óránkénti alapon.

**Core díjszabás szerint**

A Termékváltozat díjszabása magonként állíthatja be. Ehhez csak meg kell adnia egy alapszintű díjakból az egyetlen mag és azt automatikus-számítási díjak a többi a Processzormagok száma. Írja be az árak USD-ben a portálon, és azt fogja automatikusan kiszámítsa más régiókban a díjszabása. Az árak a más régiókban használatával ellenőrizheti **díjszabási adatok exportálása**

![Core díjszabás szerint](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**Különálló díjszabása**

A díjszabás minden magok részhalmazához külön-külön Ha külön-külön díja az egyes maghoz szeretné állíthatja be.

![Különálló díjszabása](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**Exportálási-importálási díjszabása**

Exportálás díjszabás rugalmas, amely a portálon a módosításokat az excel-felületen keresztül lett konfigurálva van. Ez lehetővé teszi, hogy ellenőrizze a régiók szerinti díjszabás és a helyi pénznemben díjszabása.
Kattintson a **exportálás díjszabás** tölti le a díjszabás előre fel van töltve egy excel-fájlt. Lesz szerkesztése ezek az excel programban, majd **importálás díjszabás** elvégzett módosítások importálásához.
Az importált díjszabás fogja tartalmazni, valamint a portálon.

A díjszabási Excelből a különböző régiók árai helyi pénznemben számított láthatók. Használjuk a árfolyam naponta frissülnek.

![Exportálási-importálási árfolyam példákkal díjszabása](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   Árak nem módosítható, miután egy ajánlatot élesíti. Azonban előfordulhat, hogy továbbra is kell támogatott régiók hozzáadása vagy eltávolítása.
>-   A fizetendő díj mellett a felhasználó díját [Azure\'s virtuális gépek díjszabását](http://aka.ms/vmpricingdetails).
>-   Árak a helyi pénznemben árak beállítás idején a rendelkezésre álló pénznem díjszabás használatával vannak beállítva minden régió esetében.
>-   Állítsa be, vagy külön-külön mindegyik régióhoz ár megtekintése, exportálja a díjszabási táblázatot, és importálja az egyéni díjszabás.

## <a name="vm-images"></a>Virtuálisgép-rendszerképek

Hajtsa végre a következő szakaszban lesz a Virtuálisgép-rendszerképek szakaszban. Mielőtt továbblépne ebben a szakaszban a, szüksége lesz a virtuális Merevlemezt, amely készen közzé szeretné tenni. Az alábbiakban az egyes hivatkozások annak megakadályozása, hogy a virtuális merevlemez létrehozása:

-   [Virtuálisgép-lemezkép létrehozása az Azure Marketplace a műszaki előfeltételei](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Létrehozásával és a egy Linux VHD feltöltése](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [& Tesztelje egy rendszerképből egy Linux rendszerű virtuális gép létrehozása](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Létrehozása és egy Windows VHD feltöltése ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [& Tesztelje egy rendszerképből egy Windows virtuális gép létrehozása](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Virtuális merevlemez létrehozása során tapasztalt gyakori hibáinak elhárítása](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

Miután a virtuális merevlemez kész, indítsa el ebben a szakaszban beküldésekor.
Az alábbiakban néhány adatát egyes mezőit.

### <a name="recommended-vm-sizes"></a>Javasolt Virtuálisgép-méretek

Válasszon legfeljebb hat javasolt Virtuálisgép-méretet. Ezek a javaslatok az Azure Marketplace-en és az Azure Portalon a Tarifacsomag panelen amikor megvásárlása és a rendszerkép üzembe helyezése mellett döntenek jelennek meg. **Ezek csak ajánlások. Az ügyfél el tudja válasszon, amely megfelelő a lemezképben megadott lemezek bármely Virtuálisgép-méretet.**  Az alábbi képernyőfelvételen látható, hogy egy ügyfél jelenik meg az Azure Portalon javasolt Virtuálisgép-méretek.


![Javasolt Virtuálisgép-méretek](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>Nyitott portok

Adja meg a portokat, amelyet végzett megnyitva és elérhető. Ezeket a portokat a virtuális gépek üzembe helyezése során.

### <a name="adding-vm-images"></a>Virtuálisgép-rendszerkép hozzáadása

A következő lépés, hogy egy Virtuálisgép-lemezkép adja meg a Termékváltozat. Termékváltozat száma legfeljebb 8 lemez verziók is hozzáadhat. Csak a legmagasabb szintű lemez verzió számát egy adott termékváltozat jelennek meg az Azure Marketplace-en. API-kon keresztül látható lesz mások.

A **lemez verziója**válassza **+ új verzió**. Ez azt a következő mezők kitöltése szükséges.

#### <a name="vm-image-version"></a>VM-lemezkép verziója

A Virtuálisgép-lemezkép verziója szükséges a [sémantická verze](http://semver.org/) formátumban. Verziók az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lenniük. A lemezképek különböző termékváltozatban különböző fő- és alverzió verziók is rendelkezhet. A Termékváltozat belül verziók csak kell lennie a növekményes változásokat, amely növelheti a javítást (X.Y.Z a – Z).

#### <a name="os-vhd-url"></a>OS VHD URL-CÍME

Adja meg a [közös hozzáférésű jogosultságkód URI Azonosítójának](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) az operációs rendszer virtuális Merevlemeze számára létrehozott.

Ha ez a Termékváltozat társított adatlemezek vannak, ezek a lemezek hozzáadása kiválasztásával választhat a **+ új adatlemez** hivatkozásra. Ez a művelet, hogy töltse ki további mezőket jeleníti meg.

#### <a name="lun-vhd-url"></a>LUN VIRTUÁLIS MEREVLEMEZ URL-CÍME

Adja meg a [közös hozzáférésű jogosultságkód URI Azonosítójának](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) az adatokat lemezen.

#### <a name="lun-number"></a>Logikai egység száma

Rendelje hozzá a LUN számot. Ez a szám az adatlemezt, ez a Termékváltozat a számára lefoglalt lesz.

>[!Note]
>Egy adott virtuális gép verzióját és az adatlemezeket a Termékváltozat a közzététel után zárja magát, és nem módosítható. Minden további VM-verziók, amely hozzáadja a termékváltozatra támogatásához szükséges adatlemezek száma nem módosítható.

#### <a name="common-sas-url-issues--fixes"></a>Gyakori problémák SAS URL-címe & javítások

| Probléma                                                                 | Üzenet                                                                           | Javítás                                                           |  Dokumentációra mutató hivatkozással                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| Hiba történt a Másolás-lemezkép - "?" nem található a SAS URL-címe                | Hiba: A lemezképek másolása. Nem sikerült letölteni a blob használatával a megadott SAS URI-t.       | Frissítés az SAS URL-cím használata ajánlott eszközök                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Hiba történt a Másolás lemezkép - "st" és "se" paraméterek nem az SAS URL-címe   | Hiba: A lemezképek másolása. Nem sikerült letölteni a blob használatával a megadott SAS URI-t.        | A kezdő és záró dátuma, az SAS URL-cím frissítése             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Hiba történt a lemezképek – "sp = rl" nem az SAS URL-cím másolása                    | Hiba: A lemezképek másolása. Nem sikerült letölteni a blob SAS URI-t használó megadott         | Beállítás "Olvasási" & "List engedélyeket a SAS URL-cím frissítése     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Hiba történt a lemezképek - SAS URL-cím másolása térközt rendelkezik a virtuális merevlemez neve     | Hiba: A lemezképek másolása. Nem sikerült letölteni a blob használatával a megadott SAS URI-t.        | Frissítse a SAS URL-címét, szóközök nélkül                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Hiba történt a lemezképek – SAS URL-engedélyezési hiba másolása               | Hiba: A lemezképek másolása. Töltse le a blob engedélyezési hiba miatt nem sikerült     | Az SAS URL-címet generálni                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>A Marketplace-en konfigurálása

Konfigurálja a mezőket, amelyek az ajánlat jelennek meg a Marketplace-en nézete segítségével [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és a [az Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Előfizetés azonosítókat előzetes verzió

Az Azure előfizetés azonosítókat, amelyeket meg szeretne hozzáférése az ajánlat az ajánlat közzétételekor listája. Ezek fehér felsorolt előfizetések lehetővé teszik, hogy a hozzászólásláncnak ajánlat így előtt tesztelje élő. A partner portál lehetővé teszi legfeljebb 100 előfizetést engedélyezési listára.

### <a name="suggested-categories"></a>Javasolt kategóriák

A megadott listáról, hogy az ajánlat társítható legjobb legfeljebb 5 kategóriák kiválasztása A kiválasztott kategóriákra lesz használható az ajánlat leképezése elérhető termékkategóriák [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és [az Azure Portal](https://portal.azure.com/).

Az alábbi példák bemutatják a Piactérrel kapcsolatos információkat az Azure Marketplace-en és az Azure Portalon.

**Az Azure Marketplace-en**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>Emblémáinak

Kövesse a Cloud Partner portálra feltöltött emblémák:

-   Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.

-   Az Azure Portal a témák színei fehér, és fekete. Ne használja ezeket a színeket, a emblémák háttérszíne. Használjon színt, amely biztosítja, a emblémák neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát.

    >[!Note] 
    >Ha használ áttetsző háttérrel, majd győződjön meg arról, hogy a emblémák szöveg nem fehér, fekete vagy a kék.

-   Ne használjon színátmenetes hátteret az emblémában.

-   Szöveg elkerüli az embléma. Ez magában foglalja az Ön vállalatát vagy márkanév. Az embléma megjelenését és működését kell lennie *egybesimított* és átmenetekhez kerülendő.

-   Az embléma nem archiválhatók.

#### <a name="hero-logo"></a>Hero-embléma

A Hero embléma megadása nem kötelező. A közzétevő nem szeretné Hero emblémát tölthet fel. Azonban az embléma követően nem törölhető. A partner be kell tartaniuk az Azure Marketplace-en irányelveket Hero ikonok.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Útmutató a Hero embléma ikon

-   A közzétevő megjelenített neve, csomag címét és az ajánlat hosszú összegzése jelennek meg a fehér színű betűtípus használatával. Ne használja a bármely világos szín a háttérben. Fekete, a fehér és áttetsző háttérrel Hero ikonok esetén nem engedélyezett.

-   A közzétevő megjelenített név, cím, az ajánlat hosszú összegzése és a Létrehozás gombra vannak beágyazva programozott módon a Hero embléma az ajánlat felsorolt tervet. Ne adja meg a szöveg, amikor az identitásfelügyelet a Hero embléma. Hagyja meg az üres területet a jobb oldalon az embléma. Ez a terület 415 x 100 képpont legyen, és 370 ellensúlyozza a balról képpont.

![Példa a főképemblémára](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>Felügyeleti vezethet

Az ajánlat az érdeklődő beállítások konfigurálásához kövesse az [ezek az utasítások](./cloud-partner-portal-get-customer-leads.md).

## <a name="to-configure-support"></a>Támogatási konfigurálása

A támogatási nézet használatával adja meg a következő információkat:

- Támogató kapcsolattartók a cégtől, például a mérnöki csapathoz.
- Ügyfél-támogatási kapcsolattartók.

## <a name="to-publish-the-offer"></a>Az ajánlat közzététele

Az utolsó lépés, hogy az ajánlat közzététele. Hajtsa végre a [ezeket az utasításokat az előfizetési csomag keretében élesben](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md).
