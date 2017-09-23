---
title: "Titkosítás az Azure Data Lake Store-ban | Microsoft Docs"
description: "A titkosítás és a kulcsrotálás működésének megismerése az Azure Data Lake Store-ban"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 20444d368c568ee716ff242e33323b91ffd198eb
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Az adatok titkosítása az Azure Data Lake Store-ban

A titkosítás az Azure Data Lake Store-ban segíti az adatok védelmét, vállalati biztonsági szabályzatok implementálását és az előírt megfelelőségi követelmények teljesítését. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti az implementálás egyes technikai aspektusait.

A Data Lake Store az adatok titkosítását inaktív adatok és adatátvitel esetén egyaránt támogatja. Inaktív adatok esetén a Data Lake Store csak az alapértelmezés szerinti, átlátható titkosítást támogatja. Kicsit részletesebben kifejtve ez az alábbiakat jelenti:

* **Alapértelmezés szerint bekapcsolva**: Új Data Lake Store-fiók létrehozásakor az alapértelmezés szerinti beállítás engedélyezi a titkosítást. Ezután a Data Lake Store-ban tárolt adatokat a rendszer mindig titkosítja, mielőtt állandó adathordozón tárolná. Minden adatnál ez lesz a viselkedés, és ez nem módosítható egy fiók létrehozása után.
* **Transzparens**: A Data Lake Store automatikusan titkosítja az adatokat a tárolás előtt, és mindig visszafejti az adatokat lekérés előtt. A titkosítást egy rendszergazda konfigurálja és felügyeli a Data Lake Store szintjén. Az adathozzáférési API-k nem módosulnak. Ezért a titkosítás miatt nincs szükség módosításokra a Data Lake Store-ral kommunikáló alkalmazásokban és szolgáltatásokban.

Az átvitt adatok (azaz a mozgásban lévő adatok) titkosítása is mindig a Data Lake Store-ban történik. Amellett, hogy az adatok titkosítása az állandó adathordozón való tárolás előtt történik meg, az átvitt adatok is mindig titkosítva vannak HTTPS segítségével. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez. Az alábbi ábra bemutatja, hogy a Data Lake Store hogyan titkosítja az adatokat.

![A Data Lake Store-ban végbemenő adattitkosítás ábrája](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Titkosítás beállítása a Data Lake Store-ral

A Data Lake Store titkosításának beállítása mindig a fiók létrehozása során történik, és alapértelmezés szerint minden esetben engedélyezve van. A kulcsokat kezelheti saját maga, vagy hagyhatja, hogy a Data Lake Store kezelje őket Ön helyett (ez az alapértelmezett beállítás).

További részletekért lásd [az első lépéseket](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>A titkosítás működésének megismerése a Data Lake Store-ban

A következőkben megismerheti a titkosítási főkulcsok kezelésének módját és a Data Lake Store-ban adattitkosításhoz használható három különböző kulcstípust.

### <a name="master-encryption-keys"></a>Titkosítási főkulcsok

A Data Lake Store a titkosítási főkulcsok (Master Encryption Key, MEK) kezelését kétféle módon biztosítja. Jelen esetben azt feltételezzük, hogy a titkosítási főkulcs a legfelső szintű kulcs. A Data Lake Store-ban tárolt adatok visszafejtéséhez szükség van a titkosítási főkulcsra.

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
|Kié a Key Vault-példány és a titkosítási főkulcs?|A Data Lake Store szolgáltatás|A Key Vault-példány az Öné, és az Ön Azure-előfizetéséhez tartozik. A Key Vaultban lévő MEK szoftver vagy hardver által felügyelt lehet.|
|Visszavonható a Data Lake Store szolgáltatáshoz tartozó MEK hozzáférése?|Nem|Igen. Az ügyfelek kezelhetik a Key Vault hozzáférés-vezérlési listáit, és eltávolíthatják a Data Lake Store szolgáltatáshoz tartozó szolgáltatásidentitás hozzáférés-vezérlési bejegyzéseit.|
|Törölhető véglegesen a titkosítási főkulcs?|Nem|Igen. Ha az ügyfél törli a MEK-et a Key Vaultból, a Data Lake Store-fiók adatait senki nem tudja majd visszafejteni, még a Data Lake Store szolgáltatás sem. <br><br> Ha készült kifejezett biztonsági mentés a MEK-ről a Key Vaultból történő törlést megelőzően, akkor a kulcs, majd pedig az adatok visszaállíthatók. Ha azonban nem készült biztonsági mentés a MEK-ről a Key Vaultból történő törlést megelőzően, akkor a Data Lake Store-fiók adatait a továbbiakban soha nem lehet már visszafejteni.|


Ezen, a MEK kezelőjét és a Key Vault-példány elhelyezkedését illető eltérésen kívül a kialakítás a két mód esetében megegyezik.

A titkosítási főkulcsok kezelési módjának megválasztásakor fontos szem előtt tartani a következőket:

*   A Data Lake Store-fiók létrehozásakor kiválaszthatja, hogy felhasználó által vagy a szolgáltatás által kezelt kulcsokat kíván-e használni.
*   Ez a mód a Data Lake Store-fiókok üzembe helyezése után nem módosítható.

### <a name="encryption-and-decryption-of-data"></a>Adattitkosítás és -visszafejtés

Az adattitkosítás során háromféle kulcsot használunk. A következő táblázat az összefoglalást tartalmazza:

| Kulcs                   | Rövidítés | Társítva ezzel: | Tárolási hely                             | Típus       | Megjegyzések                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Titkosítási főkulcs | MEK          | Data Lake Store-fiók | Key Vault                              | Aszimmetrikus | Kezelheti a Data Lake Store, vagy Ön is.                                                              |
| Adattitkosítási kulcs   | DEK          | Data Lake Store-fiók | Állandó tároló, a Data Lake Store szolgáltatás által kezelve | Szimmetrikus  | A DEK titkosítását a MEK végzi. A szolgáltatás a titkosított DEK-et tárolja az állandó adathordozón. |
| Blokktitkosítási kulcs  | BEK          | Egy adatblokk | None                                         | Szimmetrikus  | A blokktitkosítási kulcsot az adattitkosítási kulcsból és az adatblokkból származtatjuk.                                                      |

Az alapelveket a következő ábra mutatja be:

![Adattitkosítási kulcsok](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>A fájl visszafejtésekor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Store-fiók adattitkosítási kulcsa gyorsítótárazva van-e és használatra kész-e.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  A fájl minden adatblokkja esetében:
    - Olvassa ki a titkosított adatblokkot az állandó tárolóból.
    - Hozza létre a blokktitkosítási kulcsot az adattitkosítási kulcsból és a titkosított adatblokkból.
    - A blokktitkosítási kulcs használatával fejtse vissza az adatokat.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Az adatblokk titkosításakor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Store-fiók adattitkosítási kulcsa gyorsítótárazva van-e és használatra kész-e.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  Hozzon létre egy egyedi blokktitkosítási kulcsot az adatblokk számára az adattitkosítási kulcsból.
3.  AES-256 titkosítással végezze el a blokktitkosítási kulccsal ellátott adatblokk titkosítását.
4.  A titkosított adatblokk az állandó tárolóban lesz tárolva.

> [!NOTE] 
> A titkosítatlan adattitkosítási kulcsot a rendszer a teljesítmény javítása érdekében egy rövid időre gyorsítótárazza a memóriában, és annak elteltével azonnal törli azt. Az állandó adathordozón a tárolás mindig a titkosítási főkulcs által titkosítva történik.

## <a name="key-rotation"></a>Kulcsrotálás

Felhasználó által kezelt kulcsok használata esetén a titkosítási főkulcs rotálható. A Data Lake Store-fiók felhasználó által kezelt kulcsokkal történő beállításáért lásd [az első lépéseket](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Előfeltételek

A Data Lake Store-fiók beállításánál a saját kulcsok használatát választotta. A fiók létrehozása után ez a beállítás már nem módosítható. Az alábbi lépések során azt feltételezzük, hogy felhasználó által kezelt (tehát a Key Vaultból saját maga által kiválasztott) kulcsokat használ.

Vegye figyelembe, hogy az alapértelmezett titkosítási beállítások használatakor az adatok titkosítása mindig a Data Lake Store által kezelt kulcsokkal történik. Ez esetben nincs lehetőség a kulcsok rotálására, mivel azokat a Data Lake Store kezeli.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>A titkosítási főkulcs rotálása a Data Lake Store-ban

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Nyissa meg a Data Lake Store-fiókhoz társított kulcsokat tároló Key Vaultot. Válassza a **Kulcsok** elemet.

    ![Képernyőkép a Key Vaultról](./media/data-lake-store-encryption/keyvault.png)

3.  Válassza ki a Data Lake Store-fiókhoz társított kulcsot, és hozza létre annak egy új verzióját. A Data Lake Store kizárólag az új kulcsverzióra történő kulcsrotálást támogatja. A más kulcsra történő kulcsrotálás nem támogatott.

   ![Képernyőkép a Kulcsok ablakról, amelyen az Új verzió elem van kiemelve](./media/data-lake-store-encryption/keynewversion.png)

4.  Nyissa meg a Data Lake Store-tárfiókot, és válassza a **Titkosítás** elemet.

    ![Képernyőkép a Data Lake Store-tárfiók ablakról, amelyen a Titkosítás van kiemelve](./media/data-lake-store-encryption/select-encryption.png)

5.  Megjelenik egy tájékoztató üzenet arról, hogy a kulcs egy új verziója érhető el. A kulcs új verzióra történő frissítéséhez kattintson a **Kulcs rotálása** lehetőségre.

    ![Képernyőkép a Data Lake Store ablakról, amelyen az üzenet és a Kulcs rotálása van kiemelve](./media/data-lake-store-encryption/rotatekey.png)

Ez a művelet kevesebb mint két percet vehet igénybe, és a kulcsrotálás nem jár várt leállással. A művelet befejezését követően a kulcs új verziója lesz használatban.

