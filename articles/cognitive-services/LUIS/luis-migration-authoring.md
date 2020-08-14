---
title: Migrálás Azure Resource authoring-kulcsba
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, hogyan lehet áttelepíteni a Language Understanding (LUIS) szerzői hitelesítését egy e-mail-fiókból egy Azure-erőforrásba.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.author: diberry
ms.openlocfilehash: 390080fecefcc22decd8bec70d043b28311a725d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212634"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrálás Azure Resource authoring-kulcsba

Language Understanding (LUIS) a létrehozási hitelesítés egy e-mail-fiókból egy Azure-erőforrásra módosult. Bár jelenleg nem szükséges, az Azure-erőforrásra való áttérés a jövőben érvénybe lép.


## <a name="what-is-migration"></a>Mi az áttelepítés?

A Migrálás folyamata az e-mail-fiókból egy Azure-erőforrásra történő szerzői hitelesítés módosítására szolgál. A fiók a Migrálás után egy Azure-előfizetéshez és egy Azure authoring-erőforráshoz lesz társítva. *Végül az összes LUIS felhasználót (tulajdonos vagy közreműködő) át kell telepítenie.*

Az áttelepítést a LUIS portálról kell elvégezni. Ha például a LUIS CLI használatával hozza létre a szerzői kulcsokat, az áttelepítési folyamatot a LUIS portálon kell végrehajtania. A Migrálás után továbbra is rendelkezhet az alkalmazásokkal, de ezek az alkalmazások szintjének helyett az Azure-erőforrás szintjén lesznek hozzáadva.

> [!Note]
> A Migrálás előtt a szerzők a LUIS-alkalmazás szintjén _közreműködőként_ ismertek. Az áttelepítés után a _közreműködő_ Azure-szerepköre ugyanarra a funkcióra van felhasználva az Azure-erőforrás szintjén.

## <a name="note-before-you-migrate"></a>Az áttelepítés előtt vegye figyelembe a következőt:

* Az authoring Experience **-et november, 2, 2020**-ig kell áttelepítenie. 
* Az áttelepítés egy egyirányú folyamat. Az áttelepítés után nem mehet vissza.
* Ha Ön az alkalmazás tulajdonosa, a rendszer automatikusan áttelepíti az alkalmazásokat.
* A tulajdonos nem választhat az áttelepítendő alkalmazások egy részhalmazát, és a folyamat nem vonható vissza.
* Az alkalmazások a tulajdonos migrálása után törlődnek a közreműködő oldaláról.
* A rendszer a tulajdonosoktól kéri az e-mailek küldését a közreműködők számára, hogy tájékoztassa őket az áttelepítésről.
* Az alkalmazások nem lesznek áttelepítve, ha Ön az alkalmazásban közreműködő.
* A tulajdonos nem tudja, hogy a közreműködők migrálva vannak.
* Az áttelepítés nem gyűjt automatikusan közreműködőket, és nem helyezi át vagy adja hozzá őket az Azure authoring-erőforráshoz. Az alkalmazás tulajdonosa az, akinek az áttelepítés után el kell végeznie ezt a lépést. Ehhez a lépéshez [engedélyekre van szükség az Azure authoring-erőforráshoz](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Miután a közreműködők hozzá lettek rendelve az Azure-erőforráshoz, át kell térniük az alkalmazásokhoz való hozzáférésre. Ellenkező esetben nem férhetnek hozzá az alkalmazások létrehozásához.
* Az áttelepített felhasználók nem vehetők fel az alkalmazás munkatársának.
* Ha egy másik felhasználó tulajdonában lévő alkalmazásokhoz társított előrejelzési kulcsok vannak rendelve, ez letiltja a tulajdonos és a közreműködők áttelepítését. Tekintse meg a cikk későbbi részében ismertetett javaslatokat.

> [!Note]
> Ha előrejelzés-futtatókörnyezeti erőforrást kell létrehoznia, a létrehozásához [külön folyamat](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) szükséges.

## <a name="migration-prerequisites"></a>Migrálás előfeltételei

* Érvényes Azure-előfizetéssel kell társítania. Kérje meg a bérlői rendszergazdát, hogy vegye fel Önt az előfizetésbe, vagy [regisztráljon egy ingyenes](https://azure.microsoft.com/free/)verzióra.
* Létre kell hoznia egy LUIS Azure authoring-erőforrást a LUIS portálról vagy a Azure Portalról. A LUIS-portálon létrehozott authoring-erőforrások a következő szakaszban tárgyalt áttelepítési folyamat részét képezik.
* Ha az alkalmazásokban közreműködő, az alkalmazások nem lesznek automatikusan áttelepítve. Javasoljuk, hogy az alkalmazások biztonsági mentését az [exportálással](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)vagy az API-k használatával. A Migrálás után visszaimportálhatja az alkalmazást a LUIS-ba. Az importálási folyamat létrehoz egy új alkalmazást egy új alkalmazás-AZONOSÍTÓval, amelynek Ön a tulajdonosa.
* Ha Ön az alkalmazás tulajdonosa, nem kell exportálnia az alkalmazásokat, mert azok automatikusan áttelepülnek. Javasoljuk, hogy mentse az egyes alkalmazások közreműködői listáját. A listát tartalmazó e-mail-sablon opcionálisan az áttelepítési folyamat részeként is elérhető.


|Portál|Cél|
|--|--|
|[Azure](https://azure.microsoft.com/free/)| Hozzon létre előrejelzési és létrehozási erőforrásokat.<br> Közreműködők kiosztása erőforrásokhoz.|
|[LUIS](https://www.luis.ai)| Migrálás új szerzői erőforrásokra.<br> Új authoring-erőforrások létrehozása az áttelepítési folyamat során.<br> Az **Manage**  >  **Azure-erőforrások** kezelése oldalon megadhatja vagy megadhatja az alkalmazások előrejelzési és szerzői erőforrásainak hozzárendelését. <br> Alkalmazások áthelyezése egy authoring-erőforrásból egy másikba.  |

> [!Note]
> A LUIS-alkalmazás készítése ingyenes, ahogy az a F0 szinten is szerepel. [További információ a díjszabási szintekről](luis-limits.md#key-limits).


## <a name="migration-steps"></a>A migrálás lépései

1. A LUIS-portálon, amelyen dolgozik, megkezdheti az áttelepítési folyamatot az **Azure** ikonról a felső eszköztáron.

   > [!div class="mx-imgBorder"]
   > ![Áttelepítés ikonja](./media/migrate-authoring-key/migration-button.png)

2. Az áttelepítés előugró ablakával folytathatja az áttelepítést, vagy később is áttelepítheti azokat. Válassza az **áttelepítés most**lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Az áttelepítési folyamat első előugró ablaka, ahol az áttelepítés most lehetőséget választja](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Ha bármelyik alkalmazás rendelkezik közreműködővel, a rendszer arra kéri, hogy küldjön nekik egy e-mailt, amely lehetővé teszi számukra az áttelepítés megismerését. Ez egy választható lépés.

   Az egyes közreműködők és alkalmazások esetében az alapértelmezett e-mail-alkalmazás egy egyszerűsített formátumú e-mail-címmel nyílik meg. A küldés előtt szerkesztheti az e-maileket. Az e-mail-sablon tartalmazza az alkalmazás AZONOSÍTÓját és az alkalmazás nevét.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```

   > [!Note]
   > Miután áttelepítette a fiókját az Azure-ba, az alkalmazások már nem lesznek elérhetők a közreműködők számára.

4. Ha bármely alkalmazásban közreműködő, akkor a rendszer az áttelepítési folyamat során a beállítás kiválasztásával kéri az alkalmazások egy példányának exportálását. Ez egy választható lépés.

   Ha bejelöli a beállítást, a következő oldal jelenik meg. Válassza a bal oldali Letöltés gombokat a kívánt alkalmazások exportálásához. Ezeket az alkalmazásokat az áttelepítés után vissza is importálhatja, mivel azok nem lesznek automatikusan áttelepítve.

   > [!div class="mx-imgBorder"]
   > ![Az alkalmazások exportálásának kérése.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Dönthet úgy, hogy létrehoz egy új LUIS authoring-erőforrást, vagy áttelepíti egy meglévő szerzői erőforrásra, ha már létrehozott egyet az Azure-ból. Válassza ki a kívánt lehetőséget az alábbi gombok egyikének kiválasztásával.

   > [!div class="mx-imgBorder"]
   > ![Új authoring-erőforrás létrehozásához és meglévő authoring-erőforrással való használatához szükséges gombok](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Új authoring-erőforrás létrehozása a LUIS-ből az áttelepíteni

Ha új authoring-erőforrást szeretne létrehozni, válassza az **új szerzői erőforrás létrehozása** lehetőséget, és a következő ablakban adja meg a következő információkat. Ezután válassza a **Done** (Kész) elemet.

> [!div class="mx-imgBorder"]
> ![Szerzői erőforrások létrehozására szolgáló ablak](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Bérlő neve**: az a bérlő, amelyhez az Azure-előfizetése társítva van. Ez alapértelmezés szerint a jelenleg használt bérlőre van beállítva. A bérlőket átválthatja a jobb szélső ikon kiválasztásával, amely a monogramját tartalmazza.
* **Erőforrás neve**: A kiválasztott egyéni név. Az URL-cím részeként használatos a szerzői műveletek és előrejelzési végpontok lekérdezéséhez.
* **Előfizetés neve**: az erőforráshoz társítandó előfizetés. Ha egynél több előfizetéssel rendelkezik, amely a bérlőhöz tartozik, válassza ki a kívánt elemet a legördülő listából.
* **Azure-erőforráscsoport neve**: a legördülő listából kiválasztott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.

Vegye figyelembe, hogy régiónként akár 10 ingyenes Author-erőforrást is használhat, előfizetések szerint. Ha az előfizetése több mint 10 szerzői erőforrást tartalmaz ugyanabban a régióban, akkor nem fog tudni újat létrehozni.

A szerzői erőforrás létrehozásakor megjelenik a siker üzenet. Az előugró ablak bezárásához kattintson a **Bezárás** gombra.

  > [!div class="mx-imgBorder"]
  > ![Az authoring-erőforrást jelző üzenet sikeresen létrejött](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>Meglévő authoring-erőforrás használata az áttelepíteni

Ha az előfizetése már társítva van egy LUIS authoring Azure-erőforrással, vagy ha létrehozott egy erőforrást a Azure Portalból, és nem egy újat szeretne létrehozni, válassza a **meglévő authoring Resource használata**lehetőséget. Adja meg a következő adatokat a következő ablakban, majd válassza a **kész**lehetőséget.

> [!div class="mx-imgBorder"]
>![Meglévő authoring-erőforrás módosítására szolgáló ablak](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Bérlő neve**: az a bérlő, amelyhez az Azure-előfizetése társítva van. Ez alapértelmezés szerint a jelenleg használt bérlőre van beállítva.
* **Előfizetés neve**: az erőforráshoz társítandó előfizetés. Ha egynél több előfizetéssel rendelkezik, amely a bérlőhöz tartozik, válassza ki a kívánt elemet a legördülő listából.
* **Erőforrás neve**: az áttelepíteni kívánt szerzői erőforrás.

> [!Note]
> Ha nem látja a szerzői erőforrást a legördülő listában, akkor győződjön meg arról, hogy a megfelelő helyen hozta létre azt a LUIS-portálon, ahol be van jelentkezve. Győződjön meg arról is, hogy az Ön által létrehozott szerzői erőforrás, nem pedig előrejelzési erőforrás.


Érvényesítse a szerzői erőforrás nevét, majd kattintson az **áttelepítés** gombra.

> [!div class="mx-imgBorder"]
> ![A szerzői erőforrás kiválasztására szolgáló ablak, amely most már elérhető a Migrálás gombbal](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Megjelenik egy sikeres üzenet. Az előugró ablak bezárásához kattintson a **Bezárás** gombra.

> [!div class="mx-imgBorder"]
> ![Üzenet, amely szerint a szerzői erőforrás migrálása sikeres volt](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Alkalmazások használata az áttelepítés után

Az áttelepítési folyamat után minden olyan LUIS-alkalmazást, amelynek Ön a tulajdonosa, most már egyetlen LUIS authoring-erőforráshoz lesz hozzárendelve.

A **saját alkalmazások** lista az új szerzői erőforrásba áttelepített alkalmazásokat jeleníti meg. Az alkalmazásokhoz való hozzáférés előtt válassza ki az előfizetés és a LUIS authoring Resource elemet a megjelenő alkalmazások megtekintéséhez.

 > [!div class="mx-imgBorder"]
 > ![Az előfizetés és az authoring erőforrás mezői](./media/create-app-in-portal-select-subscription-luis-resource.png)

Nem kell tudnia, hogy az authoring Resource kulcsa az alkalmazások a LUIS Portalon való szerkesztésének folytatásához szükséges.

Ha programozott módon szeretné szerkeszteni az alkalmazásokat, szüksége lesz a szerzői kulcs értékeire. Ezek az értékek az **Manage**  >  **Azure-erőforrások** kezelése lapon jelennek meg a Luis portálon. A Azure Portal az erőforrás **kulcsai** oldalon is elérhetők. Létrehozhat több szerzői erőforrást is, és hozzárendelheti őket ugyanabból a lapról.

 > [!div class="mx-imgBorder"]
 > ![Lap szerzői erőforrások kezeléséhez](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Közreműködők hozzáadása az erőforrások létrehozásához

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Ismerje meg [, hogyan adhat hozzá közreműködőket](luis-how-to-collaborate.md) a szerzői erőforrásokhoz. A közreműködők hozzáférhetnek az adott erőforráshoz tartozó összes alkalmazáshoz.

Az adott erőforrás **Access Control (iam)** lapján hozzáadhat közreműködőket a szerzői erőforráshoz Azure Portal. További információ: [közreműködői hozzáférés hozzáadása](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Ha a LUIS-alkalmazás tulajdonosa át lett telepítve, és hozzáadta a közreműködőt az Azure-erőforráshoz, akkor a közreműködő továbbra sem férhet hozzá az alkalmazáshoz, kivéve, ha azok migrálása is megtörténik.

## <a name="luis-portal-migration-reminders"></a>LUIS-portál áttelepítési emlékeztetők

A [Luis-portál](https://www.luis.ai) biztosítja az áttelepítési folyamatot.

Ha mindkét feltétel teljesül, a rendszer áttelepíti a következőt:
* Az e-mail-hitelesítési rendszeren alkalmazásokat készít a szerzői műveletekhez.
* Ön az alkalmazás tulajdonosa.

Heti rendszerességgel kéri az alkalmazások átadását. Ezt az ablakot Migrálás nélkül is lezárhatja. Ha a következő ütemezett időszak előtt kíván áttelepítést végezni, megkezdheti az áttelepítési folyamatot a LUIS-portál felső eszköztárán található **Azure** ikon használatával.

## <a name="prediction-resources-blocking-migration"></a>Az áttelepítést blokkoló erőforrások előrejelzése
Az áttelepítés negatívan befolyásolja az alkalmazások futtatókörnyezetét. A Migrálás során a rendszer minden közreműködőt eltávolít az alkalmazásaiból, és más alkalmazásokból is el lesz távolítva közreműködőként. Ez a folyamat azt jelenti, hogy a közreműködők által hozzárendelt kulcsok is törlődnek, ami megszakíthatja az alkalmazást, ha éles környezetben van. Ennek az az oka, hogy letiltjuk az áttelepítést, amíg manuálisan nem távolítja el a velük társított munkatársakat vagy kulcsokat.

Az áttelepítés le van tiltva, ha az alábbi feltételek bármelyike igaz:

* A nem a saját alkalmazásokban rendelt előrejelzési/futtatókörnyezeti erőforrásokat.
* Más felhasználók is rendelhetnek előrejelzési/futtatókörnyezeti erőforrásokat az Ön tulajdonában lévő alkalmazásokhoz.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Ajánlott lépések, ha Ön az alkalmazás tulajdonosa
Ha egyes alkalmazások tulajdonosaként van társítva, és egy előrejelzési/futtatókörnyezeti kulcsot rendelt hozzájuk az alkalmazásokhoz, az áttelepítés során hiba jelenik meg. A hiba felsorolja azokat az alkalmazás-azonosítókat, amelyekhez más felhasználók tulajdonában lévő előrejelzési kulcsok vannak rendelve.

A következő megoldást javasoljuk:
* Munkatársak értesítése az áttelepítésről.
* Távolítsa el az összes közreműködőt a hibában látható alkalmazásokból.
* Az áttelepítési folyamaton át kell esnie, amelynek sikeresnek kell lennie, ha manuálisan eltávolítja a közreműködőket.
* Közreműködők társítása az új authoring-erőforráshoz. A közreműködők áttelepítik és újra hozzárendelik az előrejelzési erőforrásokat az alkalmazásokhoz. Vegye figyelembe, hogy ez az alkalmazás átmeneti megszakítást okoz, amíg újra nem rendeli hozzá az előrejelzési erőforrásokat.

Itt van egy másik lehetséges megoldás. A tulajdonos áttelepítése előtt a közreműködők a Azure Portal az Azure-előfizetésekben közreműködőként vehetik fel az alkalmazás tulajdonosait. Ez a lépés megadja a tulajdonos hozzáférését a futásidejű előrejelzési erőforráshoz. Ha a tulajdonos az új előfizetéssel lett áttelepítve (amely egy új bérlő alatt található), akkor ez a lépés nem csak a közreműködő és az alkalmazás-tulajdonos áttelepítési folyamatát oldja fel. Emellett az alkalmazások zökkenőmentes áttelepítését is lehetővé teszi, ha az előrejelzési kulcs továbbra is hozzá van rendelve, és nem szakítja meg az alkalmazásokat.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Ajánlott lépések, ha egy alkalmazásban munkatársa van
Ha együttműködik az alkalmazásokkal, és egy előrejelzési/futtatókörnyezeti kulcsot rendelt hozzá ezekhez az alkalmazásokhoz, az áttelepítés során hiba jelenik meg. A hiba felsorolja az áttelepítést blokkoló alkalmazás-azonosítókat és fő elérési utakat.

A következő megoldást javasoljuk:
* Alkalmazások exportálása biztonsági másolatként. Ez egy opcionális lépés az áttelepítési folyamatban.
* Az előrejelzési erőforrások hozzárendelésének megszüntetése **Manage**az  >  **Azure-erőforrások** kezelése lapon.
* Az áttelepítési folyamaton át kell esnie.
* Alkalmazások importálása az áttelepítés után.
* Az **Manage**  >  **Azure-erőforrások** kezelése oldalon rendelje hozzá az előrejelzési kulcsokat az alkalmazásaihoz.

> [!Note]
> Amikor az áttelepítést követően importálja az alkalmazásokat, különböző alkalmazás-azonosítókkal fog rendelkezni. Ettől eltérőek lesznek az éles környezetben is. Ekkor az alkalmazások tulajdonosa lesz.

## <a name="troubleshooting-the-migration-process"></a>Az áttelepítési folyamat hibaelhárítása

Amikor megkísérli az áttelepítést, de nem találja az Azure-előfizetését a legördülő listában:
* Győződjön meg arról, hogy rendelkezik olyan érvényes Azure-előfizetéssel, amely jogosult Cognitive Services erőforrások létrehozására. Lépjen a [Azure Portalra](https://ms.portal.azure.com) , és keresse meg az előfizetés állapotát. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/cognitive-services/).
* Győződjön meg arról, hogy az érvényes előfizetéséhez társított megfelelő bérlő van. Ezen az eszköztáron átválthatja a bérlőket az avatárról balra: az ![ eszköztáron, ahol átválthatja a bérlőket](./media/migrate-authoring-key/switch-user-tenant-2.png)

Ha meglévő authoring-erőforrással rendelkezik, de nem találja, akkor a **meglévő authoring Resource használata** lehetőség kiválasztásával:
* Az erőforrás valószínűleg olyan helyen lett létrehozva, amely eltér a portálon, ahol be van jelentkezve. Keresse meg a [Luis authoring régiókat és portálokat](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions).
* Ehelyett hozzon létre egy új erőforrást a LUIS-portálon.

Ha bejelöli az **új szerzői erőforrás létrehozása** lehetőséget, és a következő hibaüzenet jelenik meg: "nem sikerült beolvasni a felhasználó Azure-adatait, próbálkozzon újra később":
* Az előfizetés régiónként legalább 10 Author-erőforrással rendelkezhet. Ebben az esetben nem fog tudni új authoring-erőforrást létrehozni.
* Az áttelepítéshez válassza a **meglévő authoring Resource használata** lehetőséget, és válassza ki az előfizetéshez tartozó meglévő erőforrások egyikét.

Ha a következő hibaüzenetet látja, ellenőrizze az [ajánlott lépéseket, ha Ön az alkalmazás tulajdonosa](#recommended-steps-if-youre-the-owner-of-the-app).
![Hiba, amely azt mutatja, hogy az áttelepítés nem sikerült a tulajdonosoknál](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Ha a következő hibaüzenet jelenik meg, akkor tekintse meg az [ajánlott lépéseket, ha egy alkalmazásban közreműködő](#recommended-steps-if-youre-a-collaborator-on-an-app).
![Hiba, amely azt mutatja, hogy az áttelepítés nem sikerült a közreműködők számára](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Következő lépések

* Tekintse át [a szerzői és futtatókörnyezeti kulcsokkal kapcsolatos fogalmakat](luis-how-to-azure-subscription.md).
* Tekintse át a [kulcsok hozzárendelését](luis-how-to-azure-subscription.md) és a [közreműködők hozzáadását](luis-how-to-collaborate.md)ismertető témakört.
