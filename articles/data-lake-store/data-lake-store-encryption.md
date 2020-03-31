---
title: Titkosítás az Azure Data Lake Storage Gen1 szolgáltatásban | Microsoft dokumentumok
description: Az Azure Data Lake Storage Gen1 titkosítása segít az adatok védelmében, a vállalati biztonsági szabályzatok megvalósításában és a jogszabályi megfelelőségi követelmények teljesítésében. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti az implementálás egyes technikai aspektusait.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878395"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Adatok titkosítása az Azure Data Lake Storage Gen1 szolgáltatásban

Az Azure Data Lake Storage Gen1 titkosítása segít az adatok védelmében, a vállalati biztonsági szabályzatok megvalósításában és a jogszabályi megfelelőségi követelmények teljesítésében. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti az implementálás egyes technikai aspektusait.

A Data Lake Storage Gen1 támogatja az adatok titkosítását mind nyugalmi, mind átvitel közben. Az inaktív adatok esetében a Data Lake Storage Gen1 támogatja az "alapértelmezés szerint" átlátszó titkosítást. Kicsit részletesebben kifejtve ez az alábbiakat jelenti:

* **Alapértelmezés szerint:** Amikor új Data Lake Storage Gen1 fiókot hoz létre, az alapértelmezett beállítás engedélyezi a titkosítást. Ezt követően a Data Lake Storage Gen1-ben tárolt adatok mindig titkosítva vannak az állandó adathordozón való tárolás előtt. Minden adatnál ez lesz a viselkedés, és ez nem módosítható egy fiók létrehozása után.
* **Átlátszó**: A Data Lake Storage Gen1 automatikusan titkosítja az adatokat a megpersistnivaló előtt, és visszafejti az adatokat a lekérés előtt. A titkosítás konfigurálása és kezelése a Data Lake Storage Gen1 fiók szintjén egy rendszergazda által. Az adathozzáférési API-k nem módosulnak. Így nincs szükség módosításokra a Data Lake Storage Gen1 titkosítás miatt a Data Lake Storage Gen1-tel interakcióba lépő alkalmazásokban és szolgáltatásokban.

Az átvitel alatt lévő adatok (más néven mozgásban lévő adatok) mindig titkosítva vannak a Data Lake Storage Gen1-ben. Amellett, hogy az adatok titkosítása az állandó adathordozón való tárolás előtt történik meg, az átvitt adatok is mindig titkosítva vannak HTTPS segítségével. A HTTPS az egyetlen protokoll, amely a Data Lake Storage Gen1 REST-összeköttetések támogatott. Az alábbi ábra bemutatja, hogyan lesz nek titkosítva az adatok a Data Lake Storage Gen1 szolgáltatásban:

![Adattitkosítás diagramja a Data Lake Storage Gen1 szolgáltatásban](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Titkosítás beállítása a Data Lake Storage Gen1 szolgáltatással

A Data Lake Storage Gen1 titkosítása a fiók létrehozása során van beállítva, és alapértelmezés szerint mindig engedélyezve van. A kulcsokat saját kezűleg kezelheti, vagy engedélyezheti, hogy a Data Lake Storage Gen1 kezelje őket (ez az alapértelmezett).

További részletekért lásd [az első lépéseket](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>A titkosítás működése a Data Lake Storage Gen1 szolgáltatásban

Az alábbi információk ismertetik, hogyan kezelheti a fő titkosítási kulcsok, és ismerteti a három különböző típusú kulcsok at használhatja az adattó-tároló gen1 adattitkosítás.

### <a name="master-encryption-keys"></a>Titkosítási főkulcsok

A Data Lake Storage Gen1 két módot biztosít a fő titkosítási kulcsok (MEK) kezelésére. Jelen esetben azt feltételezzük, hogy a titkosítási főkulcs a legfelső szintű kulcs. A Data Lake Storage Gen1-ben tárolt adatok visszafejtéséhez hozzáférés szükséges a fő titkosítási kulcshoz.

A titkosítási főkulcs kezelésének kétféle módja a következő:

*   Szolgáltatás által kezelt kulcsok
*   Felhasználó által kezelt kulcsok

A titkosítási főkulcs mindkét mód esetében az Azure Key Vaultban van biztonságosan tárolva. A Key Vault az Azure teljes körűen felügyelt, magas biztonsági szinten lévő szolgáltatása, amely biztosítja a titkosítási kulcsok védelmét. További információkért lásd a [Key Vaultról](https://azure.microsoft.com/services/key-vault) szóló cikket.

Az alábbiakban röviden összehasonlítjuk a kétféle mód MEK-kezelési képességét.

|  | Szolgáltatás által kezelt kulcsok | Felhasználó által kezelt kulcsok |
| --- | --- | --- |
|Hogyan történik az adatok tárolása?|Mindig titkosítva a tárolást megelőzően.|Mindig titkosítva a tárolást megelőzően.|
|Hol történik a titkosítási főkulcs tárolása?|Key Vault|Key Vault|
|Létezik-e titkosítatlanul, a Key Vaulton kívül tárolt titkosítási kulcs? |Nem|Nem|
|Beolvasható a MEK a Key Vaultba?|Nem. A MEK a Key Vaultban történő tárolása után kizárólag titkosításra és visszafejtésre használható.|Nem. A MEK a Key Vaultban történő tárolása után kizárólag titkosításra és visszafejtésre használható.|
|Kié a Key Vault-példány és a titkosítási főkulcs?|A Data Lake Storage Gen1 szolgáltatás|A Key Vault-példány az Öné, és az Ön Azure-előfizetéséhez tartozik. A Key Vaultban lévő MEK szoftver vagy hardver által felügyelt lehet.|
|Visszavonhatja a Hozzáférést a MEK-hoz a Data Lake Storage Gen1 szolgáltatáshoz?|Nem|Igen. A Hozzáférés-vezérlési listákat kezelheti a Key Vaultban, és eltávolíthatja a Data Lake Storage Gen1 szolgáltatás identitásához tartozó hozzáférés-vezérlési bejegyzéseket.|
|Törölhető véglegesen a titkosítási főkulcs?|Nem|Igen. Ha törli a MEK-t a Key Vaultból, a Data Lake Storage Gen1 fiók adatait senki sem tudja visszafejteni, beleértve a Data Lake Storage Gen1 szolgáltatást is. <br><br> Ha készült kifejezett biztonsági mentés a MEK-ről a Key Vaultból történő törlést megelőzően, akkor a kulcs, majd pedig az adatok visszaállíthatók. Ha azonban nem tett biztonsági másolatot a MEK-ról a Key Vaultból való törlés előtt, a Data Lake Storage Gen1 fiókban lévő adatokat ezt követően soha nem lehet visszafejteni.|


Ezen, a MEK kezelőjét és a Key Vault-példány elhelyezkedését illető eltérésen kívül a kialakítás a két mód esetében megegyezik.

A titkosítási főkulcsok kezelési módjának megválasztásakor fontos szem előtt tartani a következőket:

*   A Data Lake Storage Gen1 fiók kiépítésekor kiválaszthatja, hogy ügyfél által kezelt kulcsokat vagy szolgáltatás által felügyelt kulcsokat használjon.You can choose whether to use customer managed keys or service managed keys when you provision a Data Lake Storage Gen1 account.
*   A Data Lake Storage Gen1 fiók kiépítése után a mód nem módosítható.

### <a name="encryption-and-decryption-of-data"></a>Adattitkosítás és -visszafejtés

Az adattitkosítás során háromféle kulcsot használunk. A következő táblázat az összefoglalást tartalmazza:

| Kulcs                   | Rövidítés | Társítva ezzel: | Tárolási hely                             | Típus       | Megjegyzések                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Titkosítási főkulcs | MEK          | Egy Data Lake Storage Gen1 fiók | Key Vault                              | Aszimmetrikus | A Data Lake Storage Gen1 vagy Ön által kezelhető.                                                              |
| Adattitkosítási kulcs   | DEK          | Egy Data Lake Storage Gen1 fiók | Állandó tároló, a Data Lake Storage Gen1 szolgáltatás által kezelt | Szimmetrikus  | A DEK titkosítását a MEK végzi. A szolgáltatás a titkosított DEK-et tárolja az állandó adathordozón. |
| Blokktitkosítási kulcs  | BEK          | Egy adatblokk | None                                         | Szimmetrikus  | A blokktitkosítási kulcsot az adattitkosítási kulcsból és az adatblokkból származtatjuk.                                                      |

Az alapelveket a következő ábra mutatja be:

![Adattitkosítási kulcsok](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>A fájl visszafejtésekor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Storage Gen1 fiók DEK-je gyorsítótárazott-e, és készen áll-e a használatra.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  A fájl minden adatblokkja esetében:
    - Olvassa ki a titkosított adatblokkot az állandó tárolóból.
    - Hozza létre a blokktitkosítási kulcsot az adattitkosítási kulcsból és a titkosított adatblokkból.
    - A blokktitkosítási kulcs használatával fejtse vissza az adatokat.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Az adatblokk titkosításakor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Storage Gen1 fiók DEK-je gyorsítótárazott-e, és készen áll-e a használatra.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  Hozzon létre egy egyedi blokktitkosítási kulcsot az adatblokk számára az adattitkosítási kulcsból.
3.  AES-256 titkosítással végezze el a blokktitkosítási kulccsal ellátott adatblokk titkosítását.
4.  A titkosított adatblokk az állandó tárolóban lesz tárolva.

> [!NOTE] 
> A MEK mindig titkosítva tárolja az adattitkosítási kulcsot, függetlenül attól, hogy a tárolás állandó adathordozón vagy a memória-gyorsítótárban történik.

## <a name="key-rotation"></a>Kulcsrotálás

Felhasználó által kezelt kulcsok használata esetén a titkosítási főkulcs rotálható. A Data Lake Storage Gen1 fiók ügyféláltal kezelt kulcsokkal történő beállításáról [az Első lépések.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

### <a name="prerequisites"></a>Előfeltételek

A Data Lake Storage Gen1 fiók beállításakor a saját kulcsok használatát választotta. A fiók létrehozása után ez a beállítás már nem módosítható. Az alábbi lépések során azt feltételezzük, hogy felhasználó által kezelt (tehát a Key Vaultból saját maga által kiválasztott) kulcsokat használ.

Vegye figyelembe, hogy ha az alapértelmezett titkosítási beállításokat használja, az adatok mindig titkosítva lesznek a Data Lake Storage Gen1 által kezelt kulcsok használatával. Ebben a beállításban nem rendelkezik a kulcsok elforgatására, mivel azokkal a Data Lake Storage Gen1 kezeli őket.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>A MEK elforgatása a Data Lake Storage Gen1-ben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Tallózással keresse meg a Key Vault-példányt, amely a Data Lake Storage Gen1 fiókjához társított kulcsokat tárolja. Válassza a **Billentyűk**lehetőséget.

    ![Képernyőkép a Key Vaultról](./media/data-lake-store-encryption/keyvault.png)

3. Válassza ki a Data Lake Storage Gen1 fiókjához társított kulcsot, és hozzon létre egy új verziót a kulcsból. Vegye figyelembe, hogy a Data Lake Storage Gen1 jelenleg csak támogatja a kulcs rotációját egy kulcs új verziójára. A más kulcsra történő kulcsrotálás nem támogatott.

   ![Képernyőkép a Kulcsok ablakról, amelyen az Új verzió elem van kiemelve](./media/data-lake-store-encryption/keynewversion.png)

4. Tallózással keresse meg a Data Lake Storage Gen1 fiókot, és válassza a **Titkosítás**lehetőséget.

   ![Képernyőkép a Data Lake Storage Gen1 fiókablakról, kiemelve a Titkosítás](./media/data-lake-store-encryption/select-encryption.png)

5. Megjelenik egy tájékoztató üzenet arról, hogy a kulcs egy új verziója érhető el. A kulcs új verzióra történő frissítéséhez kattintson a **Kulcs rotálása** lehetőségre.

   ![Képernyőkép a Data Lake Storage Gen1 ablakról, amelyen az üzenet és a Kulcs elforgatása van kiemelve](./media/data-lake-store-encryption/rotatekey.png)

Ez a művelet kevesebb mint két percet vehet igénybe, és a kulcsrotálás nem jár várt leállással. A művelet befejezését követően a kulcs új verziója lesz használatban.

> [!IMPORTANT]
> A kulcsrotáció után a rendszer nem használja tovább aktívan a kulcs régi verzióját az adatok titkosítására.  Az olyan ritka esetekben, amikor a váratlan meghibásodások az adatok redundáns másolatait is érinthetik, lehetséges, hogy az adatokat egy olyan biztonsági másolatból kell visszaállítani, amely továbbra is a régi kulcsot használja. Annak érdekében, hogy az adatok az ilyen ritka esetekben is elérhetők maradjanak, őrizze meg a titkosítási kulcsa előző verzióját. Tekintse meg [a vész-helyreállítási útmutató a Data Lake Storage Gen1 a](data-lake-store-disaster-recovery-guidance.md) katasztrófa-helyreállítási tervezés ajánlott eljárásokat. 