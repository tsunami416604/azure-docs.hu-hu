---
title: Csomagok létrehozása virtuálisgép-ajánlathoz az Azure Marketplace-en
description: Megtudhatja, hogyan hozhat létre terveket egy virtuálisgép-ajánlathoz az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: bc5e98484560fcc15e0ea3e289069c84687f158c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040539"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Csomagok létrehozása virtuálisgép-ajánlathoz

A partner centeren belül a különböző csomagokra vonatkozó lehetőségek is megadhatók. Az ajánlathoz legalább egy csomagra (korábbi nevén SKU) van szükség, amely a bevételi közönség, az Azure-régió, a funkciók vagy a virtuálisgép-lemezképek esetében változhat.

Az egyes ajánlatokhoz legfeljebb 100 csomagot hozhat létre: ezek közül legfeljebb 45 lehet privát. További információ a [Microsoft kereskedelmi piactéren](private-offers.md)elérhető privát csomagokról.

A csomagok létrehozása után válassza a **terv áttekintés** fület a megjelenítéshez:

- Csomag neve
- Licencelési modellek
- Közönség (nyilvános vagy privát)
- Jelenlegi közzétételi állapot
- Elérhető műveletek

A **terv áttekintő** ablaktábláján elérhető műveletek a csomag aktuális állapotától függően változhatnak.

- Ha a terv állapota vázlat, válassza a **Piszkozat törlése** lehetőséget.
- Ha a terv állapota élőben van közzétéve, válassza az **eladási terv leállítása** vagy a **privát célközönség szinkronizálása** lehetőséget.

## <a name="create-a-new-plan"></a>Új csomag létrehozása

Válassza a felül az **új csomag létrehozása** lehetőséget. Megjelenik az **új terv** párbeszédpanel.

A **terv azonosítója** mezőben hozzon létre egy egyedi díjcsomag-azonosítót az ajánlat minden egyes csomagjában. Ez az azonosító a termék webcímen lévő ügyfelek számára látható lesz. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásokat, illetve legfeljebb 50 karaktert használjon.

> [!NOTE]
> A terv azonosítója a **Létrehozás** gombra kattintva nem módosítható.

A **terv neve** mezőbe írja be a csomag nevét. Az ügyfelek akkor látják ezt a nevet, amikor döntik el, hogy melyik tervet kívánják kiválasztani az ajánlaton belül. Hozzon létre egy egyedi nevet, amely egyértelműen rámutat a csomagok közötti különbségekre. Például megadhatja a **Windows Servert** utólagos *Pay-as-you-go* elszámolású, *BYOL* , *speciális* és *vállalati* csomagokkal.

Válassza a **Létrehozás** lehetőséget.

## <a name="plan-setup"></a>Csomag beállítása

Állítsa be a terv típusának magas szintű konfigurációját, adja meg, hogy az újrahasznosítsa-e egy másik csomag technikai konfigurációját, és azonosítsa azokat az Azure-régiókat, ahol a terv elérhető. Az itt megadott beállítások határozzák meg, hogy mely mezők jelenjenek meg ugyanazon csomag más ablaktábláján.

### <a name="reuse-a-technical-configuration"></a>Technikai konfiguráció újbóli használata

Ha több azonos típusú csomaggal rendelkezik, és a csomagok azonosak egymás között, akkor kiválaszthatja, hogy a **terv újrahasznosítja a technikai konfigurációt egy másik csomagból**. Ez a beállítás lehetővé teszi, hogy az ajánlathoz tartozó más csomagok valamelyikét kiválassza, és lehetővé teszi a technikai konfigurációjának újbóli használatát.

> [!NOTE]
> Ha egy másik csomag technikai konfigurációját használja, a teljes **technikai konfiguráció** lap eltűnik ebből a csomagból. A csomagra vonatkozó technikai konfiguráció részleteit, beleértve a jövőbeli frissítéseket is, a tervhez is használni fogjuk. Ez a beállítás a terv közzététele után nem módosítható.

### <a name="azure-regions"></a>Azure-régiók

A tervet legalább egy Azure-régióban elérhetővé kell tenni.

Válassza ki az **Azure globális** lehetőséget, hogy a tervet elérhetővé tegye az összes olyan globális Azure-régióban, amely kereskedelmi Piactéri integrációt tartalmaz. További információ: a [földrajzi Elérhetőség és a pénznemek támogatása](marketplace-geo-availability-currencies.md).

Válassza a **Azure Government** lehetőséget, hogy a terv elérhető legyen a [Azure Government](../azure-government/documentation-government-welcome.md) régióban. Ez a régió szabályozott hozzáférést biztosít az Egyesült Államok szövetségi, állami, helyi vagy törzsi szerveinek ügyfeleinek, valamint az azok kiszolgálására jogosult partnereinknek. Ön, mint közzétevő, felelős a megfelelőségi ellenőrzésért, a biztonsági intézkedésekért és az ajánlott eljárásokhoz. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az USA-ban található).

Mielőtt közzéteszi a [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), tesztelje és érvényesítse a tervet a környezetben, mert bizonyos végpontok eltérőek lehetnek. A csomag beállításához és teszteléséhez kérjen egy próbaverziós fiókot a [Microsoft Azure Government próbaverzió](https://azure.microsoft.com/global-infrastructure/government/request/) oldaláról.

> [!NOTE]
> Miután közzétette és elérhetővé tette a csomagot egy adott Azure-régióban, nem távolíthatja el a régiót.

### <a name="azure-government-certifications"></a>Azure Government minősítések

Ez a beállítás csak akkor látható, ha az előző szakaszban az Azure-régiót választotta **Azure Government** .

Azure Government szolgáltatások kezelik bizonyos kormányzati előírások és követelmények hatálya alá eső adatmennyiséget. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amely leírja azokat. Ezek lehetnek közvetlenül a programra mutató hivatkozások vagy a saját webhelyein való megfelelőség leírására mutató hivatkozások. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="plan-listing"></a>Csomag listázása

Ebben a szakaszban a csomag listázási részleteit konfigurálja. Ez a panel a konkrét információkat jeleníti meg, amelyek eltérőek lehetnek az azonos ajánlatban található többi csomagtól.

### <a name="plan-name"></a>Csomag neve

A rendszer automatikusan kitölti ezt a mezőt azzal a névvel, amelyet a tervének létrehozásakor adott meg. Ez a név jelenik meg az Azure Marketplace-en a csomag címeként. Legfeljebb 100 karakter hosszú lehet.

### <a name="plan-summary"></a>Csomag összegzése

Adja meg a csomag rövid összefoglalását, nem az ajánlatot. Ez az Összegzés legfeljebb 100 karakter hosszú lehet.

### <a name="plan-description"></a>Csomag leírása

Írja le, hogy mi teszi ezt a csomagot egyedivé, és ismertesse az ajánlaton belüli csomagok közötti különbségeket. Csak a csomag leírása, nem az ajánlat. A csomag leírása legfeljebb 2 000 karaktert tartalmazhat.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Ezen az ablaktáblán a következőket konfigurálja:

- Azok a piacok, ahol ez a csomag elérhető. Minden csomagnak legalább egy [piacon](marketplace-geo-availability-currencies.md)elérhetőnek kell lennie.
- Az óránkénti díj.
- Azt határozza meg, hogy a terv mindenki számára elérhető legyen-e, vagy csak bizonyos ügyfelek számára (privát közönség számára).

### <a name="markets"></a>Piacok

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Jelölje be az összes olyan piaci hely jelölőnégyzetét, ahol a csomag megvásárolható. (Ezekben a piacokon a felhasználók továbbra is üzembe helyezhetik az ajánlatot a ["csomag beállítása"](#plan-setup) szakaszban kiválasztott összes Azure-régióban.) Az **átutalt adó** gomb azokat az országokat/régiókat jeleníti meg, amelyekben a Microsoft az Ön nevében kifizeti az értékesítési és használati adót. A Kínában való közzététel olyan csomagokra korlátozódik, amelyek *ingyenesek* vagy *saját licencek* (BYOL).

Ha már beállította a csomag árát az amerikai dollár (USD) pénznemben, és újabb piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A módosítások mentése után tekintse át a díjszabást az **export Prices (xlsx)** lehetőség kiválasztásával.

Ha eltávolít egy piacot, az adott piacon aktív üzemelő példányokat használó ügyfelek nem hozhatnak létre új központi telepítéseket, és nem méretezhetők a meglévő telepítések. A meglévő központi telepítések nincsenek hatással a rendszerre.

### <a name="pricing"></a>Díjszabás

A **licencelési modell** esetében válassza a **használati alapú havi számlázott csomag** lehetőséget a csomag díjszabásának konfigurálásához, vagy válassza a **saját licenc** használata lehetőséget, hogy az ügyfelek ezt a csomagot használják meglévő licenccel.

A használati alapú havi számlás csomag esetében a következő három díjszabási bejegyzés közül választhat:

- **/Mag** : az USD-es alapszintű díjszabást adja meg. A Microsoft a jelenlegi árfolyam alapján kiszámítja a díjszabást az alapméret szerint, és helyi pénznemre alakítja.
- **/Alapméret** : az USD-ben elérhető alapméretet biztosító díjszabás. A Microsoft kiszámítja a díjszabást, és helyi pénznemre konvertálja az aktuális árfolyam használatával.
- **/Piac és alapméret** : az összes piac alapméretének díjszabását adja meg. Az árakat importálhatja egy táblázatból.

> [!NOTE]
> Mentse a díjszabási változásokat, hogy engedélyezze a díjszabási adatexportálást. Miután közzétette a csomag egy piacának árát, később nem módosítható. Annak érdekében, hogy a közzététel előtt a díjak megfelelőek legyenek, exportálja a díjszabási táblázatot, és tekintse át az árakat az egyes piacokon.

### <a name="free-trial"></a>Ingyenes próba

Az ügyfeleknek egy-vagy három hónapos vagy hat hónapos *ingyenes próbaverziót* is kínálunk.

### <a name="visibility"></a>Visibility (Látótávolság)

Megtervezheti, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy kijelölt közönség számára. Az Azure-előfizetési azonosítók használatával társíthat tagságokat ebben a korlátozott célközönségben.

**Nyilvános** : a terv mindenki számára megtekinthető.

**Privát célközönség** : a terv csak az előkiválasztott közönség számára látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy megváltoztathatja nyilvánosra. Miután megnyitotta a csomagot, nyilvánosnak kell maradnia. Nem módosítható egy privát csomagra.

> [!NOTE]
> A privát vagy korlátozott célközönség nem azonos az **előnézeti ablaktáblán** meghatározott előnézeti célközönséggel. Az előzetes verzió célközönsége az Azure Marketplace-en való élő közzététel _előtt_ elérheti az ajánlatát. Bár a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes magán-és nyilvános csomagot megtekintheti érvényesítés céljából.

**Korlátozott célközönség (Azure-előfizetési azonosítók)** : rendelje hozzá azt a célközönséget, amely hozzáférhet ehhez a privát csomaghoz az Azure-előfizetési azonosítók használatával. Igény szerint az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk leírását is megadhatja. Akár 10 előfizetés-azonosítót is hozzáadhat manuálisan vagy akár 20 000 azonosítóhoz, ha CSV-táblázatot importál. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és minden betűnek kisbetűsnek kell lennie.

>[!Note]
>A privát ajánlatok nem támogatottak a Cloud Solution Provider program (CSP) viszonteladóján keresztül létesített Azure-előfizetések esetében.

### <a name="hide-a-plan"></a>Csomag elrejtése

Ha a virtuális gépet csak közvetetten kell használni, ha egy másik megoldási sablonon vagy felügyelt alkalmazáson keresztül hivatkozik rá, jelölje be ezt a jelölőnégyzetet a virtuális gép közzétételéhez, de az olyan ügyfelektől, akik közvetlenül keresnek vagy böngészhetnek.

> [!NOTE]
> A rejtett csomagok nem támogatják az előzetes verziójú hivatkozásokat.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Adja meg a csomaghoz társított képeket és egyéb technikai tulajdonságokat. További információ: a [virtuális gép ajánlati adatainak konfigurálása](azure-vm-create-listing.md).

> [!NOTE]
> A **technikai konfiguráció** lap nem jelenik meg, ha úgy konfigurálta ezt a csomagot, hogy a csomagok újrafelhasználását egy másik csomagból a **terv beállítása** lapon.

### <a name="operating-system"></a>Operációs rendszer

Az **operációs rendszer** panelen tegye a következőket:

- **Operációsrendszer-család** esetén válassza ki a **Windows** vagy **Linux** operációs rendszert.
- A **kiadás** vagy a **szállító** esetében válassza a Windows kiadás vagy a Linux gyártó elemet.
- Az operációs rendszer **felhasználóbarát neve** mezőben adjon meg egy felhasználóbarát operációsrendszer-nevet. Ez a név jelenik meg az ügyfelek számára.

### <a name="recommended-vm-sizes"></a>Ajánlott virtuálisgép-méretek

Az Azure piactéren megjelenítendő legfeljebb hat ajánlott virtuálisgép-méret közül választhat.

### <a name="open-ports"></a>Portok megnyitása

Nyisson meg nyilvános vagy magánhálózati portot egy telepített virtuális gépen.

### <a name="storage-option-for-deployment"></a>Tárolási lehetőség az üzembe helyezéshez

A **lemez központi telepítése beállításnál** válassza ki, hogy az ügyfelek milyen típusú központi telepítést használhatnak a virtuális géphez. A Microsoft javasolja, hogy csak a **felügyelt lemezes telepítésre** korlátozza a központi telepítést.

### <a name="properties"></a>Tulajdonságok

A **gyorsított hálózatkezelés támogatásához** válassza ki, hogy a virtuális gép támogatja-e a [gyorsított hálózatkezelést](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Generációk

A virtuális gép létrehozása határozza meg az általa használt virtuális hardvert. Az ügyfél igényei alapján közzétehet egy 1. generációs virtuális gépet, 2. generációs virtuális gépet vagy mindkettőt.

1. Új ajánlat létrehozásakor válasszon egy **létrehozási típust** , és adja meg a kért rendszerkép részleteit:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details.png" alt-text="A generáció legördülő listájának nézete":::

2. Ha egy másik generációt szeretne hozzáadni egy csomaghoz, válassza a **Létrehozás hozzáadása** lehetőséget:

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="A &quot;generáció hozzáadása&quot; hivatkozás nézete.":::

    Ezután adja meg a generáció részleteit:

    :::image type="content" source="./media/create-vm/azure-vm-generations-details.png" alt-text="A generációs részletek ablakának nézete.":::

    A kiválasztott **létrehozási azonosító** az ügyfelek számára látható lesz, például a termék URL-címei és az ARM-sablonok (ha vannak ilyenek). Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon; közzététel után nem módosítható.

3. Ha olyan meglévő virtuális gépet szeretne frissíteni, amelynek 1. generációja már közzé van téve, egyszerűen szerkessze a részleteket a **technikai konfiguráció** oldalon:

    :::image type="content" source="./media/create-vm/azure-vm-generations-updating.png" alt-text="A terv technikai konfiguráció oldalának nézete.":::

Ha többet szeretne megtudni az 1. és 2. generációs képességek közötti különbségekről, tekintse meg a [2. generációs virtuális gépek támogatása az Azure](../virtual-machines/generation-2.md)-ban című témakört.

### <a name="vm-images"></a>VM-lemezképek

Adja meg a lemez verziószámát és a virtuálisgép-lemezképek megosztott elérési aláírásának (SAS) URI-JÁT. Minden virtuálisgép-rendszerképhez akár 16 adatlemezt is hozzáadhat. Egy adott beadványban csak egy új rendszerkép-verziót adjon meg. A rendszerkép közzététele után nem szerkesztheti, de törölheti is. Egy verzió törlése megakadályozza, hogy az új és a meglévő felhasználók is üzembe lehessen helyezni a törölt verzió új példányát.

- **Lemez verziója** : a megadott lemezkép verziója.
- **Sas URI** : az Azure Storage-fiók azon helye, ahová az operációs rendszer VHD-jét tárolta. A SAS URI beszerzésével kapcsolatos további információkért lásd: [a megosztott hozzáférési aláírás URI-ja](azure-vm-get-sas-uri.md)a virtuálisgép-rendszerképhez.
- Az adatlemez-lemezképek az Azure Storage-fiókokban tárolt VHD közös hozzáférési aláírási URI-k is.
- Egy csomagban való beküldéshez csak egy képet adjon hozzá.

A használt operációs rendszertől függetlenül csak a megoldás által igényelt adatlemezek minimális számát adja hozzá. Az üzembe helyezés során az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget, és térjen vissza a **terv áttekintéséhez**.

## <a name="next-steps"></a>Következő lépések

- [Előzetes verzió célközönségének hozzáadása](azure-vm-create-preview.md)
