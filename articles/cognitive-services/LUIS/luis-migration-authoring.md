---
title: Migrálás az Azure-erőforrásba 2. szerzői műveletekhez
titleSuffix: Azure Cognitive Services
description: Migrálás egy Azure authoring Resource kulcsba 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983704"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrálás Azure Resource authoring-kulcsba

Language Understanding (LUIS) a létrehozási hitelesítés egy e-mail-fiókból egy Azure-erőforrásra módosult. Noha jelenleg nem szükséges, az Azure-erőforrásra való áttérés a jövőben is érvénybe lép.


## <a name="what-is-migration"></a>Mi az áttelepítés?

A Migrálás folyamata az e-mail-fiókból egy Azure-erőforrásra történő szerzői hitelesítés módosítására szolgál. A fiók a Migrálás után egy Azure-előfizetéshez és egy Azure authoring-erőforráshoz lesz társítva. **Végül az összes LUIS felhasználót (tulajdonos vagy közreműködő) át kell telepítenie.** Az áttelepítést a LUIS portálról kell elvégezni. Ha létrehozza a szerzői kulcsokat, például a LUIS CLI-vel, akkor továbbra is el kell végeznie az áttelepítési folyamatot a LUIS portálon. A Migrálás után továbbra is rendelkezhet közös szerzőkkel az alkalmazásaiban, de ezek az alkalmazások szintjének helyett az Azure-erőforrás szintjén lesznek hozzáadva.

> [!Note]
> A Migrálás előtt a közös szerzők a LUIS-alkalmazás szintjén _közreműködőként_ ismertek. Az áttelepítés után a _közreműködő_ Azure-szerepköre ugyanazokkal a funkciókkal, de az Azure-erőforrás szintjén is használatos.

## <a name="note-before-you-migrate"></a>Az áttelepítés előtt vegye figyelembe a következőt:

* Az áttelepítés egy **egyirányú** folyamat. Az áttelepítés után nem mehet vissza.
* Ha Ön az alkalmazás **tulajdonosa** , a rendszer **automatikusan áttelepíti** az alkalmazásokat.
* A tulajdonos nem választhat az áttelepítendő alkalmazások egy részhalmazát, és a folyamat nem vonható vissza.
* Az alkalmazások a **tulajdonos migrálása**után törlődnek **a közreműködők oldaláról** .
* A rendszer a tulajdonosoktól kéri az e-mailek küldését a közreműködők számára, hogy tájékoztassa őket az áttelepítésről.
* Az alkalmazások **nem lesznek áttelepítve** , ha Ön az alkalmazásban **közreműködő** .
* Nincs mód arra, hogy a tulajdonos tudja, hogy a közreműködők migrálva lettek.
* Az **áttelepítés nem** gyűjt automatikusan közreműködőket, és nem helyezi át vagy adja hozzá őket az Azure authoring-erőforráshoz. Az alkalmazás tulajdonosa az, akinek az áttelepítés után el kell végeznie ezt a lépést. Ehhez a lépéshez [engedélyekre van szükség az Azure authoring-erőforráshoz](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Az Azure-erőforráshoz való hozzárendelés után a **közreműködőknek át kell térniük az alkalmazásokhoz való hozzáférésre**. Ellenkező esetben nem lesznek hozzáférésük az alkalmazások létrehozásához.
* Az áttelepített felhasználók nem vehetők fel az alkalmazás munkatársának.
* Ha **egy másik felhasználó tulajdonában lévő alkalmazásokhoz társított előrejelzési kulcsok vannak rendelve**, ez letiltja a tulajdonos és a közreműködők **áttelepítését** . Javaslatokért lásd alább.

> [!Note]
> Ha előrejelzés-futtatókörnyezeti erőforrást kell létrehoznia, a létrehozásához [külön folyamat](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) szükséges.

## <a name="migration-prerequisites"></a>Migrálás előfeltételei

* Érvényes Azure-előfizetéssel kell társítania. Kérje meg a bérlői rendszergazdát, hogy vegye fel Önt az előfizetésbe, vagy Regisztráljon ingyen [itt](https://azure.microsoft.com/free/).
* Létre kell hoznia egy LUIS Azure authoring-erőforrást a LUIS portálról vagy a Azure Portalról. A LUIS portál létrehozási erőforrásának létrehozása a következő szakaszban tárgyalt áttelepítési folyamat részét képezi.
* Ha az **alkalmazásokban közreműködő munkatárs**, az alkalmazások nem lesznek automatikusan áttelepítve. **Ajánlott biztonsági másolatot készíteni ezeknek az alkalmazásoknak** az exportálásával, vagy az [exportálási API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)használatával. A Migrálás után visszaimportálhatja az alkalmazást a LUIS-ba. Az importálási folyamat létrehoz egy új alkalmazást egy új alkalmazás-AZONOSÍTÓval, amelynek Ön a tulajdonosa.
* Ha Ön az **alkalmazás tulajdonosa**, akkor nem kell exportálnia az alkalmazásokat, mivel azok automatikusan migrálva lesznek. Javasoljuk, **hogy mentse az egyes alkalmazások collaborator's listáját.** A listát tartalmazó e-mail-sablon opcionálisan az áttelepítési folyamat részeként is elérhető.


|Portál|Szerep|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Hozzon létre előrejelzési és létrehozási erőforrásokat.<br>* Közreműködők társítása erőforrásokhoz.|
|[LUIS](https://www.luis.ai)|* Migrálás az új szerzői erőforrásokra.<br>* Új authoring-erőforrás létrehozása az áttelepítési folyamat során.<br>* A felügyeleti **> Azure-erőforrások** oldaláról kioszthatja vagy kioszthatja az alkalmazásokat az alkalmazások számára. <br>* Alkalmazások áthelyezése egy authoring-erőforrásból egy másikba.  |

> [!Note]
> A **Luis-alkalmazás készítése ingyenes**, a szintet is jelzi `F0` . [További információ a díjszabási szintekről](luis-limits.md#key-limits).


## <a name="migration-steps"></a>A migrálás lépései

1. A LUIS-portálon, amelyen dolgozik, megkezdheti az áttelepítési folyamatot az **Azure** ikonján a felső eszköztáron.

   > [!div class="mx-imgBorder"]
   > ![Áttelepítés ikonja](./media/migrate-authoring-key/migration-button.png)

2. Az áttelepítés előugró ablakával folytathatja az áttelepítést, vagy később is áttelepítheti azokat. Válassza az **áttelepítés most**lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Első előugró ablak az áttelepítési folyamatban válassza az áttelepítés most lehetőséget.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Ha bármelyik alkalmazás rendelkezik közreműködővel, a rendszer arra kéri, hogy **küldjön nekik egy e-mailt** , amely tájékoztatja őket az áttelepítésről. Ez egy választható lépés.

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

4. Ha bármely alkalmazásban közreműködő, akkor a rendszer arra kéri, hogy az áttelepítési folyamat során a lehetőség kiválasztásával **exportálja az alkalmazások egy példányát** . Miután kiválasztotta a lehetőséget, az alábbi oldal jelenik meg, ahol a bal oldalon található Letöltés gombra kattintva exportálhatja a kívánt alkalmazásokat. Ezeket az alkalmazásokat az áttelepítés után vissza is importálhatja, mivel azok nem lesznek automatikusan áttelepítve. Ez egy választható lépés.

   > [!div class="mx-imgBorder"]
   > ![Az alkalmazás exportálásának kérése.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Dönthet úgy, hogy létrehoz egy új LUIS authoring-erőforrást, vagy áttelepíti egy meglévő szerzői erőforrásra, ha már létrehozott egyet az Azure-ból. Válassza ki a kívánt lehetőséget az alábbi megfelelő gombra kattintva.

   > [!div class="mx-imgBorder"]
   > ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Új authoring-erőforrás létrehozása a LUIS-ből az áttelepíteni

Ha új authoring-erőforrást szeretne létrehozni, válassza az **új szerzői erőforrás létrehozása** lehetőséget, és a következő ablakban adja meg a következő információkat.

> [!div class="mx-imgBorder"]
> ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Erőforrás neve** – a kiválasztott egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használhat.
* **Előfizetés neve** – az erőforráshoz társítandó előfizetés. Ha egynél több előfizetéssel rendelkezik, amely a bérlőhöz tartozik, válassza ki a kívánt elemet a legördülő listából.
* **Erőforráscsoport** – a legördülő listából kiválasztott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.
* **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van. Ez alapértelmezés szerint a jelenleg kiválasztott bérlőre van beállítva. A bérlőket úgy válthat, hogy kiválasztja a monogramját tartalmazó jobb oldali ikont.

A fenti információk megadása után válassza a **kész**lehetőséget.

Vegye figyelembe, hogy régiónként akár 10 ingyenes Author-erőforrást is használhat, előfizetések szerint. Ha az előfizetése több mint 10 szerzői erőforrást tartalmaz ugyanabban a régióban, akkor nem fog tudni újat létrehozni.

* A szerzői erőforrás létrehozásakor megjelenik a siker üzenet. Az előugró ablak bezárásához kattintson a **Bezárás** gombra.

  > [!div class="mx-imgBorder"]
  > ![A szerzői erőforrás létrehozása sikeresen megtörtént.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Meglévő authoring-erőforrás használata az áttelepíteni

Ha az előfizetése már hozzá van rendelve egy LUIS authoring Azure-erőforráshoz, vagy ha a Azure Portal-ból lett létrehozva, és egy új erőforrás létrehozása helyett szeretne áttelepíteni, válassza a **meglévő authoring Resource használata** lehetőséget, és a következő ablakban adja meg a következő információkat.

> [!div class="mx-imgBorder"]
>![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van. Ez alapértelmezés szerint a jelenleg kiválasztott bérlőre van beállítva.
* **Előfizetés neve** – az erőforráshoz társítandó előfizetés. Ha egynél több előfizetéssel rendelkezik, amely a bérlőhöz tartozik, válassza ki a kívánt elemet a legördülő listából.
* **Erőforrás neve** – válassza ki azt a szerzői erőforrást, amelyet át szeretne telepíteni.

> [!Note]
> Ha nem látja a szerzői erőforrást a legördülő listában, győződjön meg arról, hogy a **megfelelő helyen** hozta létre a bejelentkezett Luis-portálon. Győződjön meg arról is, hogy a létrehozott szolgáltatás valóban egy **authoring erőforrás** , nem pedig **előrejelzési erőforrás**.


* Érvényesítse a szerzői erőforrás nevét, és kattintson az **áttelepítés most** gombra.

 > [!div class="mx-imgBorder"]
 > ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* Megjelenik a sikeres üzenet. Az előugró ablak bezárásához kattintson a **Bezárás** gombra.

 > [!div class="mx-imgBorder"]
 > ![A szerzői erőforrás létrehozása sikeresen megtörtént.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Alkalmazások használata az áttelepítés után

* Az áttelepítési folyamat után az összes Ön által használt LUIS-alkalmazás már egyetlen LUIS authoring-erőforráshoz lesz hozzárendelve.
* A **saját alkalmazások** lista az új szerzői erőforrásba áttelepített alkalmazásokat jeleníti meg.
* Az alkalmazásokhoz való hozzáférés előtt válassza ki az előfizetés és a LUIS authoring Resource elemet a megjelenő alkalmazások megtekintéséhez.

 > [!div class="mx-imgBorder"]
 > ![Válassza ki az előfizetés és a LUIS authoring Resource elemet a megadható alkalmazások megtekintéséhez.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Nem kell tudnia, hogy az authoring Resource kulcsa az alkalmazások a LUIS Portalon való szerkesztésének folytatásához szükséges.
* Ha programozott módon szeretné szerkeszteni az alkalmazásokat, szüksége lesz a szerzői kulcs értékeire. Ezek az értékek a LUIS **-portál Manage-> Azure-erőforrások** lapján jelennek meg, és az erőforrás **kulcsai** oldalának Azure Portal is elérhetők. Létrehozhat több szerzői erőforrást is, és hozzárendelheti őket ugyanabból a lapról.

 > [!div class="mx-imgBorder"]
 > ![Szerzői erőforrások kezelése.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Közreműködők hozzáadása erőforrások létrehozásához

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Ismerje meg [, hogyan adhat hozzá közreműködőket](luis-how-to-collaborate.md) a szerzői erőforrásokhoz. A közreműködők hozzáférhetnek az adott erőforráshoz tartozó összes alkalmazáshoz.

Az adott erőforrás **Access Control (iam)** lapján hozzáadhat közreműködőket a szerzői erőforráshoz _Azure Portal_. További információ: [közreműködői hozzáférés hozzáadása](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Ha a LUIS-alkalmazás tulajdonosa át lett telepítve, és hozzáadta a közreműködőt az Azure-erőforráshoz, akkor a közreműködő továbbra sem férhet hozzá az alkalmazáshoz, kivéve, ha azok migrálása is megtörténik.

## <a name="luis-portal-migration-reminders"></a>LUIS-portál áttelepítési emlékeztetők

A [Luis-portál](https://www.luis.ai) biztosítja az áttelepítési folyamatot.

A rendszer a következőt fogja kérni a Migrálás esetén:
* Az e-mail-hitelesítési rendszeren alkalmazásokat készít a szerzői műveletekhez.
* Ön pedig Ön az alkalmazás tulajdonosa.

Heti rendszerességgel megkéri, hogy telepítse át az alkalmazásokat. Ezt az ablakot Migrálás nélkül is megszakíthatja. Ha a következő ütemezett időszak előtt kíván áttelepítést végezni, megkezdheti az áttelepítési folyamatot az **Azure** ikonján a Luis portál felső sávján.

Az áttelepítési folyamatot késleltetve törölheti az ablakot. A rendszer rendszeres időközönként áttelepíti az áttelepítést, vagy átadja az áttelepítési határidőt. Az áttelepítési folyamat elindításához a felső navigációs sáv zárolási ikonja látható.

## <a name="prediction-resources-blocking-migration"></a>Az áttelepítést blokkoló erőforrások előrejelzése
Az áttelepítés negatívan befolyásolja az alkalmazások futtatókörnyezetét. Az áttelepítés során minden közreműködő el lesz távolítva az alkalmazásokból, és a többi alkalmazásból is együttműködik. Ez a folyamat azt jelenti, hogy a közreműködők által hozzárendelt kulcsok is törlődnek, ami megszakíthatja az alkalmazást, ha éles környezetben van. Ennek az az oka, hogy letiltjuk az áttelepítést, amíg nem távolítja el a munkatársakat vagy a hozzájuk rendelt kulcsokat manuálisan.

### <a name="when-does-prediction-resources-block-migration"></a>Mikor blokkolja a jóslat-erőforrások az áttelepítést?
* Az áttelepítés le lesz tiltva, ha a nem a saját alkalmazásokban rendelt előrejelzési/futtatókörnyezeti erőforrásokat.
* Az áttelepítés le lesz tiltva, ha más felhasználók előrejelzési/futtatókörnyezeti erőforrásokat rendelnek hozzá az Ön tulajdonában lévő alkalmazásokhoz.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Ajánlott lépések, ha Ön az alkalmazás tulajdonosa
Ha egyes alkalmazások tulajdonosaként van társítva az előrejelzési/futtatókörnyezeti kulcs az alkalmazáshoz, akkor a rendszer egy hibaüzenetet jelenít meg, amikor áttelepíti azokat az alkalmazás-azonosítókat, amelyek a más felhasználók tulajdonában lévő előrejelzési kulcsokkal rendelkeznek.

A következőket javasoljuk:
* Munkatársak értesítése az áttelepítésről.
* Távolítsa el az összes közreműködőt a hibában látható alkalmazásokból.
* Az áttelepítési folyamatnak akkor kell futnia, ha manuálisan távolítja el a közreműködőket.
* Közreműködők társítása az új authoring-erőforráshoz.
* A közreműködők áttelepítik és újra hozzárendelik az előrejelzési erőforrásokat az alkalmazásokhoz.
Vegye figyelembe, hogy ez az alkalmazás átmeneti megszakítást okoz, amíg újra nem rendeli hozzá az előrejelzési erőforrásokat.

Egy másik megoldás az, hogy a tulajdonos migrálása előtt a közreműködők felvehetik az alkalmazás tulajdonosait közreműködőként az Azure-előfizetések Azure Portal. Ez biztosítja a tulajdonos hozzáférését a futásidejű előrejelzési erőforráshoz. Ha a tulajdonos olyan új előfizetést használ, amelyhez hozzá lettek adva (amely az új bérlő alatt található), ez nem csak a közreműködők és az alkalmazás-tulajdonos áttelepítési folyamatát oldja fel, de lehetővé teszi az alkalmazások zökkenőmentes áttelepítését, ha az előrejelzési kulccsal továbbra is hozzá vannak rendelve az alkalmazások megszakításához.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Ajánlott lépések teendők, ha egy alkalmazásban közreműködő
Ha együttműködik az alkalmazásokkal, és elvégezte az előrejelzési/futtatókörnyezeti kulcs hozzárendelését az alkalmazáshoz, a rendszer a Migrálás blokkolására szolgáló alkalmazás-azonosítók és fő elérési utak listáját tartalmazó áttelepítéskor hibaüzenetet jelenít meg.

A következőket javasoljuk:
* Alkalmazások exportálása biztonsági másolatként. Ez az áttelepítési folyamat választható lépéseként van megadva.
* Az előrejelzési erőforrások hozzárendelésének megszüntetése a **Manage-> Azure-erőforrások** lapon.
* Az áttelepítési folyamaton át kell esnie.
* Alkalmazások importálása az áttelepítés után.
* Az alkalmazások **kezelése – > Azure-erőforrások** lapra újra hozzárendelheti az előrejelzési kulcsokat.

> [!Note]
> Amikor az áttelepítést követően importálja az alkalmazásokat, különböző alkalmazás-azonosítókkal fog rendelkezni, és nem fog megjelenni az éles környezetben. Ekkor az alkalmazások tulajdonosa lesz.

## <a name="troubleshooting-migration-process"></a>Az áttelepítési folyamat hibaelhárítása

Amikor megpróbálja áttelepíteni, de nem találja az Azure-előfizetését a legördülő listában:
* Győződjön meg arról, hogy rendelkezik olyan érvényes Azure-előfizetéssel, amely jogosult Cognitive Services erőforrások létrehozására. Nyissa meg [Azure Portal](https://ms.portal.azure.com) és az előfizetés állapotát. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes próbaverziót](https://azure.microsoft.com/free/).
* Győződjön meg arról, hogy az érvényes előfizetéséhez társított megfelelő bérlő található. Az alábbi eszköztáron a további bal oldali avatárból is válthat bérlőket: a ![ bérlők váltása.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Ha már rendelkezik egy meglévő szerzői erőforrással, de nem találja, akkor a "meglévő szerzői erőforrás használata" lehetőség kiválasztásával:
* Az erőforrás valószínűleg egy másik helyen lett létrehozva, mint a bejelentkezett portál. Kérjük [, ellenőrizze a Luis authoring-régiókat és a portálokat](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)
* Hozzon létre egy új erőforrást a LUIS portálról

Ha az "új szerzői erőforrás létrehozása" lehetőséget választja, és az áttelepítés sikertelen volt, a következő hibaüzenet jelenik meg: "nem sikerült beolvasni a felhasználó Azure-adatait, próbálkozzon újra később"
* Az előfizetés régiónként legfeljebb 10 erőforrás-előfizetéssel rendelkezhet. Ebben az esetben nem fog tudni új authoring-erőforrást létrehozni.
* Az áttelepítéshez válassza a "meglévő szerzői erőforrás használata" lehetőséget, és jelölje ki az előfizetéséhez tartozó meglévő erőforrások egyikét.

Ha az alábbi hibaüzenet jelenik meg, tekintse meg az [ajánlott lépéseket, ha Ön az alkalmazás szakaszának tulajdonosa] az ![ áttelepítés sikertelen a tulajdonosoknak](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Ha az alábbi hibaüzenet jelenik meg, tekintse meg az [ajánlott lépések teendők, ha egy alkalmazás szakasza munkatársa] az ![ áttelepítés sikertelen a közreműködők esetében.](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Következő lépések

* A szerzői és futtatókörnyezeti kulcsokkal kapcsolatos [fogalmak](luis-concept-keys.md) áttekintése
* [A kulcsok hozzárendelésének és a](luis-how-to-azure-subscription.md) [közreműködők](luis-how-to-collaborate.md) hozzáadásának áttekintése
