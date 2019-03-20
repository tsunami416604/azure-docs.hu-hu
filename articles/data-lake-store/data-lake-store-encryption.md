---
title: Titkosítás az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Titkosítás az Azure Data Lake Storage Gen1 segít az adatok védelme, vállalati biztonsági szabályzatok implementálását és szabályozási megfelelőségi követelmények teljesítése érdekében. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti az implementálás egyes technikai aspektusait.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098134"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 adatok titkosítása

Titkosítás az Azure Data Lake Storage Gen1 segít az adatok védelme, vállalati biztonsági szabályzatok implementálását és szabályozási megfelelőségi követelmények teljesítése érdekében. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti az implementálás egyes technikai aspektusait.

Data Lake Storage Gen1 támogatja a titkosítást, inaktív és átvitel közben is. Az inaktív adatok, Data Lake Storage Gen1 támogatja a "a alapértelmezés szerint" átlátható titkosítást. Kicsit részletesebben kifejtve ez az alábbiakat jelenti:

* **Az alapértelmezés szerint**: Amikor létrehoz egy új Data Lake Storage Gen1 fiókot, az alapértelmezett beállítás engedélyezi a titkosítást. Ezután a Data Lake Storage Gen1 tárolt adatok rendszer mindig titkosítja, mielőtt állandó adathordozón tárolná. Minden adatnál ez lesz a viselkedés, és ez nem módosítható egy fiók létrehozása után.
* **Transzparens**: Data Lake Storage Gen1 automatikusan titkosítja az adatokat, és mindig visszafejti az adatokat lekérés előtt. A titkosítás konfigurált, és a egy rendszergazda felügyeli a Data Lake Storage Gen1 fiók szintjén. Az adathozzáférési API-k nem módosulnak. Így nem kell módosítania a alkalmazásokat és szolgáltatásokat, amelyek a titkosítás miatt a Data Lake Storage Gen1 interakciót.

Átvitt adatok (más néven mozgásban lévő adatok) is mindig titkosítva, a Data Lake Storage Gen1. Amellett, hogy az adatok titkosítása az állandó adathordozón való tárolás előtt történik meg, az átvitt adatok is mindig titkosítva vannak HTTPS segítségével. HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Storage Gen1 REST-felületeihez. Az alábbi ábrán látható, hogyan titkosítja az adatokat a Data Lake Storage Gen1:

![A Data Lake Storage Gen1 adattitkosítás ábrája](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Titkosítás beállítása a Data Lake Storage Gen1

Fiók létrehozása során be van állítva a Data Lake Storage Gen1 titkosítását, és alapértelmezés szerint mindig engedélyezett. Kezelhetik a kulcsokat, vagy engedélyezze a Data Lake Storage Gen1 kezelheti azokat meg (Ez az alapértelmezett érték).

További részletekért lásd [az első lépéseket](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Titkosítás működésének megismerése a Data Lake Storage Gen1

A következő információkat tartalmazza a titkosítási főkulcsok kezelésének módját, valamint azt is használhatja a Data Lake Storage Gen1 adattitkosítási kulcsok három különböző típusú.

### <a name="master-encryption-keys"></a>Titkosítási főkulcsok

Data Lake Storage Gen1 a titkosítási főkulcsok (MEK) kezelését kétféle módon biztosítja. Jelen esetben azt feltételezzük, hogy a titkosítási főkulcs a legfelső szintű kulcs. A Data Lake Storage Gen1 tárolt adatok visszafejtéséhez szükség van a titkosítási kulccsal.

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
|Visszavonható a Data Lake Storage Gen1 szolgáltatáshoz tartozó MEK hozzáférését?|Nem|Igen. A Key Vault hozzáférés-vezérlési listák felügyelete, és távolítsa el a Data Lake Storage Gen1 szolgáltatáshoz tartozó szolgáltatásidentitás hozzáférés-vezérlési bejegyzéseit.|
|Törölhető véglegesen a titkosítási főkulcs?|Nem|Igen. Ha törli a MEK a Key Vault, a Data Lake Storage Gen1 fiókban lévő adatok nem lehet visszafejteni, bárki megtekintheti, beleértve a Data Lake Storage Gen1 szolgáltatás. <br><br> Ha készült kifejezett biztonsági mentés a MEK-ről a Key Vaultból történő törlést megelőzően, akkor a kulcs, majd pedig az adatok visszaállíthatók. Azonban ha nem készített biztonsági mentést a MEK a Key Vaultból megelőzően, a Data Lake Storage Gen1 fiókban lévő adatok is nem lehet már visszafejteni ezt követően.|


Ezen, a MEK kezelőjét és a Key Vault-példány elhelyezkedését illető eltérésen kívül a kialakítás a két mód esetében megegyezik.

A titkosítási főkulcsok kezelési módjának megválasztásakor fontos szem előtt tartani a következőket:

*   E használja az ügyfél által felügyelt kulcsok vagy a szolgáltatás által a Data Lake Storage Gen1 fiók létrehozásakor választhat.
*   A Data Lake Storage Gen1 fiók ki van építve, miután a mód nem módosítható.

### <a name="encryption-and-decryption-of-data"></a>Adattitkosítás és -visszafejtés

Az adattitkosítás során háromféle kulcsot használunk. A következő táblázat az összefoglalást tartalmazza:

| Kulcs                   | Rövidítés | Társítva ezzel: | Tárolási hely                             | Typo       | Megjegyzések                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Titkosítási főkulcs | MEK          | A Data Lake Storage Gen1 fiók | Key Vault                              | Aszimmetrikus | Data Lake Storage Gen1 vagy az Ön által kezelhető.                                                              |
| Adattitkosítási kulcs   | DEK          | A Data Lake Storage Gen1 fiók | Tartós tároláshoz, a Data Lake Storage Gen1 szolgáltatás által kezelt | Szimmetrikus  | A DEK titkosítását a MEK végzi. A szolgáltatás a titkosított DEK-et tárolja az állandó adathordozón. |
| Blokktitkosítási kulcs  | BEK          | Egy adatblokk | None                                         | Szimmetrikus  | A blokktitkosítási kulcsot az adattitkosítási kulcsból és az adatblokkból származtatjuk.                                                      |

Az alapelveket a következő ábra mutatja be:

![Adattitkosítási kulcsok](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>A fájl visszafejtésekor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Storage Gen1 fiók adattitkosítási kulcsa Gyorsítótárazva-e a gyorsítótárazott és használatra kész.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  A fájl minden adatblokkja esetében:
    - Olvassa ki a titkosított adatblokkot az állandó tárolóból.
    - Hozza létre a blokktitkosítási kulcsot az adattitkosítási kulcsból és a titkosított adatblokkból.
    - A blokktitkosítási kulcs használatával fejtse vissza az adatokat.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Az adatblokk titkosításakor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Storage Gen1 fiók adattitkosítási kulcsa Gyorsítótárazva-e a gyorsítótárazott és használatra kész.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  Hozzon létre egy egyedi blokktitkosítási kulcsot az adatblokk számára az adattitkosítási kulcsból.
3.  AES-256 titkosítással végezze el a blokktitkosítási kulccsal ellátott adatblokk titkosítását.
4.  A titkosított adatblokk az állandó tárolóban lesz tárolva.

> [!NOTE] 
> A MEK mindig titkosítva tárolja az adattitkosítási kulcsot, függetlenül attól, hogy a tárolás állandó adathordozón vagy a memória-gyorsítótárban történik.

## <a name="key-rotation"></a>Kulcsrotálás

Felhasználó által kezelt kulcsok használata esetén a titkosítási főkulcs rotálható. A Data Lake Storage Gen1 fiók beállítása az ügyfél által felügyelt kulcsokkal kapcsolatban lásd: [bevezetés](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Előfeltételek

A Data Lake Storage Gen1 fiók beállításakor a saját kulcsok használatát választotta. A fiók létrehozása után ez a beállítás már nem módosítható. Az alábbi lépések során azt feltételezzük, hogy felhasználó által kezelt (tehát a Key Vaultból saját maga által kiválasztott) kulcsokat használ.

Vegye figyelembe, hogy az alapértelmezett beállítások használata titkosításhoz, ha az adatok mindig titkosítja Data Lake Storage Gen1 által kezelt kulcsokkal. Ezt a beállítást lehetővé teszi a kulcsok rotálására, nem kell, ahogy a Data Lake Storage Gen1 kezeli őket.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 a titkosítási Főkulcs rotálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Keresse meg a Data Lake Storage Gen1 fiókhoz társított kulcsokat tároló Key vaultot. Válassza a **Kulcsok** elemet.

    ![Képernyőkép a Key Vaultról](./media/data-lake-store-encryption/keyvault.png)

3. Válassza ki a Data Lake Storage Gen1 fiókhoz társított kulcsot, és hozzon létre egy új verzióját. Vegye figyelembe, hogy Data Lake Storage Gen1 jelenleg csak támogatja a kulcs új verziója. A más kulcsra történő kulcsrotálás nem támogatott.

   ![Képernyőkép a Kulcsok ablakról, amelyen az Új verzió elem van kiemelve](./media/data-lake-store-encryption/keynewversion.png)

4. Keresse meg a Data Lake Storage Gen1 fiókot, és válassza ki **titkosítási**.

   ![A Data Lake Storage Gen1 képernyőkép ablakról, amelyen a titkosítás van kiemelve](./media/data-lake-store-encryption/select-encryption.png)

5. Megjelenik egy tájékoztató üzenet arról, hogy a kulcs egy új verziója érhető el. A kulcs új verzióra történő frissítéséhez kattintson a **Kulcs rotálása** lehetőségre.

   ![A Data Lake Storage Gen1 képernyőkép ablak az üzenetet, és a kulcs rotálása van kiemelve](./media/data-lake-store-encryption/rotatekey.png)

Ez a művelet kevesebb mint két percet vehet igénybe, és a kulcsrotálás nem jár várt leállással. A művelet befejezését követően a kulcs új verziója lesz használatban.

> [!IMPORTANT]
> A kulcsrotáció után a rendszer nem használja tovább aktívan a kulcs régi verzióját az adatok titkosítására.  Az olyan ritka esetekben, amikor a váratlan meghibásodások az adatok redundáns másolatait is érinthetik, lehetséges, hogy az adatokat egy olyan biztonsági másolatból kell visszaállítani, amely továbbra is a régi kulcsot használja. Annak érdekében, hogy az adatok az ilyen ritka esetekben is elérhetők maradjanak, őrizze meg a titkosítási kulcsa előző verzióját. Lásd: [vészhelyreállítási útmutató a Data Lake Storage Gen1 adatok](data-lake-store-disaster-recovery-guidance.md) az kapcsolatos ajánlott vészhelyreállítás-tervezési. 