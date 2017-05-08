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
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: hu-hu
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Az adatok titkosítása az Azure Data Lake Store-ban

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Az Azure Data Lake Store titkosításának áttekintése.

A titkosítás az Azure Data Lake Store-ban (ADLS) lehetővé teszi az adatok védelmét, vállalati biztonsági szabályzatok implementálását és az előírt megfelelőségi követelmények teljesítését. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti a Data Lake Store adattitkosításának technikai aspektusait.

Az ADLS támogatja az inaktív adatok alapértelmezés szerinti, átlátható titkosítását. Kicsit részletesebben kifejtve ez az alábbiakat jelenti:

* Alapértelmezés szerint bekapcsolva: Új Azure Data Lake Store-fiók létrehozásakor az alapértelmezés szerinti beállítás engedélyezi a titkosítást. Ezután a Data Lake Store-ban tárolt adatokat a rendszer mindig titkosítja, mielőtt állandó adathordozón tárolná. Minden adatnál ez lesz a viselkedés, és ez nem módosítható egy fiók létrehozása után.
* Transzparens: Az ADLS automatikusan titkosítja az adatokat a tárolás előtt, és mindig visszafejti az adatokat lekérés előtt. A titkosítást egy rendszergazda konfigurálja és felügyeli a Data Lake Store szintjén. Az adathozzáférési API-k nem módosulnak, ezért a titkosítás miatt nincs szükség módosításokra a Data Lake Store-ral kommunikáló alkalmazásokban és szolgáltatásokban.

Az átvitt adatok (azaz a mozgásban lévő adatok) titkosítása is mindig a Data Lake Store-ban történik. Amellett, hogy az adatok titkosítása az állandó adathordozón való tárolás előtt történik meg, az átvitt vagy mozgásban lévő adatok is mindig titkosítva vannak HTTPS (HTTP SSL) segítségével. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez.

![1. ábra](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Titkosítás beállítása az Azure Data Lake Store-ban

Az Azure Data Lake Store titkosításának beállítása mindig a fiók létrehozása során történik, és alapértelmezés szerint minden esetben engedélyezve van. Az ügyfelek választhatnak, hogy maguk kívánják kezelni a kulcsokat, vagy engedélyezik az Azure Data Lake Store számára azok kezelését (ez az alapértelmezés).

A titkosítás az Azure Data Lake Store-ban történő beállításához tekintse meg az [első lépéseket](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Technikai részletek – A titkosítás működésének megismerése az Azure Data Lake Store-ban

### <a name="master-encryption-keys"></a>Titkosítási főkulcsok

Az Azure Data Lake Store a titkosítási főkulcsok (Master Encryption Key, MEK) kezelését kétféle módon biztosítja. A titkosítási főkulcsok használatát részletesen egy későbbi szakaszban mutatjuk be. Jelen esetben azt feltételezzük, hogy a titkosítási főkulcs a legfelső szintű kulcs. A Data Lake Store-ban tárolt adatok visszafejtéséhez szükség van a titkosítási főkulcsra.

A titkosítási főkulcs kezelésének kétféle módja a következő:

1.    Szolgáltatás által kezelt kulcsok
2.    Felhasználó által kezelt kulcsok

A titkosítási főkulcs mindkét mód esetében az Azure Key Vaultban van biztonságosan tárolva. Az Azure Key Vault az Azure teljes körűen felügyelt, magas biztonsági szinten lévő szolgáltatása, amely biztosítja a titkosítási kulcsok védelmét. [Itt](https://azure.microsoft.com/services/key-vault) talál további információkat az Azure Key Vaultról.

Az alábbiakban röviden összehasonlítjuk a kétféle mód MEK-kezelési képességét.

|  | Szolgáltatás által kezelt kulcsok | Felhasználó által kezelt kulcsok |
| --- | --- | --- |
|Hogyan történik az adatok tárolása?|Mindig titkosítva a tárolást megelőzően|Mindig titkosítva a tárolást megelőzően|
|Hol történik a titkosítási főkulcs tárolása?|Azure Key Vault|Azure Key Vault|
|Létezik-e az Azure Key Vaulton kívül, titkosítatlanul tárolt titkosítási kulcs?|Nem|Nem|
|Beolvasható a MEK az Azure Key Vaultba?|Nem. A Key Vaultban tárolt kulcsok a későbbiekben kizárólag titkosításra és visszafejtésre használhatók.|Nem. A Key Vaultban tárolt kulcsok a későbbiekben kizárólag titkosításra és visszafejtésre használhatók.|
|Kié az Azure Key Vault és a titkosítási főkulcs?|Ez egy Azure Data Lake Store-szolgáltatás.|Az Azure Key Vault az ügyfél birtokában van, és a saját Azure-előfizetése részét képezi. A Key Vaultban lévő MEK szoftver vagy hardver (HSM) által felügyelt lehet.|
|Visszavonhatja az ügyfél az Azure Data Lake Store szolgáltatáshoz tartozó MEK hozzáférését?|Nem|Igen. Az ügyfelek kezelhetik az Azure Key Vault hozzáférés-vezérlési listáit, és eltávolíthatják az Azure Data Lake Store szolgáltatáshoz tartozó szolgáltatásidentitás hozzáférés-vezérlési bejegyzéseit.|
|Törölheti véglegesen az ügyfél a titkosítási főkulcsot?|Nem|Igen. Ha az ügyfél törli a MEK-et az Azure Key Vaultból, az ADLS-fiók adatait nem lehet majd visszafejteni, még az Azure Data Lake Store szolgáltatás által sem. <br><br> Ha az ügyfél kifejezetten biztonsági mentést készít a MEK-ről az Azure Key Vaultból történő törlést megelőzően, a kulcs, majd pedig az adatok visszaállíthatók. Ha azonban az ügyfél nem készít biztonsági mentést a MEK-ről az Azure Key Vaultból történő törlést megelőzően, akkor az ADLS-fiók adatait a továbbiakban nem lehet már visszafejteni.|


A legfelső szintű eltérésen kívül (a MEK és a Key Vault kezelőjét illetően) a kialakítás a két mód esetében megegyezik.

A titkosítási főkulcsok kezelési módjának megválasztásakor néhány fontos szempontot érdemes szem előtt tartani.

1.    Az ADLS-fiók létrehozásakor kiválaszthatja, hogy felhasználó által vagy ADLS által kezelt kulcsokat kíván-e használni
2.    Ez a mód ADLS-fiókok üzembe helyezése után nem módosítható.

### <a name="encryption-and-decryption-of-data"></a>Adattitkosítás és -visszafejtés

Az Azure Data Lake számára történő adattitkosítás során háromféle kulcsot használunk. Az alábbi táblázat ezek szerepét összegzi:

| Kulcs                   | Rövidítés | Társítva a következővel | Tárolási hely                             | Típus       | Megjegyzések                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Titkosítási főkulcs | MEK          | Egy ADLS-fiók | Azure Key Vault                              | Aszimmetrikus | ADLS vagy felhasználó által felügyelt lehet                                                              |
| Adattitkosítási kulcs   | DEK          | Egy ADLS-fiók | Állandó tároló – ADLS-szolgáltatás által felügyelt | Szimmetrikus  | A DEK titkosítását a MEK végzi, a szolgáltatás pedig az állandó adathordozón a titkosított DEK-et tárolja |
| Blokktitkosítási kulcs  | BEK          | Egy adatblokk | None                                         | Szimmetrikus  | A blokktitkosítási kulcsot az adattitkosítási kulcsból és az adatblokkból származtatjuk                                                      |

Az alapelveket a következő ábra mutatja be:

![2. ábra](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>A fájl visszafejtésekor használatos pszeudoalgoritmus:
1.    Ellenőrizze, hogy az ADLS-fiók adattitkosítási kulcsa gyorsítótárazva van-e és használatra kész-e.
    * Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el az Azure Key Vaultba. A visszafejtett adattitkosítási kulcs gyorsítótárazását követően az használatra kész.
2.    A fájl minden adatblokkja esetében
    * A titkosított adatblokkot az állandó tárolóból olvassa ki
    * A blokktitkosítási kulcsot az adattitkosítási kulcsból és a titkosított adatblokkból hozza létre
    * Az adatok visszafejtése a blokktitkosítási kulcs használatával történik
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Az adatblokk titkosításakor használatos pszeudoalgoritmus:
1.    Ellenőrizze, hogy az ADLS-fiók adattitkosítási kulcsa gyorsítótárazva van-e és használatra kész-e.
    * Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el az Azure Key Vaultba. A visszafejtett adattitkosítási kulcs gyorsítótárazását követően az használatra kész.
2.    Hozzon létre egy egyedi blokktitkosítási kulcsot az adatblokk számára az adattitkosítási kulcsból.
3.    AES-256 titkosítással végezze el a blokktitkosítási kulccsal ellátott adatblokk titkosítását.
4.    A titkosított adatblokk az állandó tárolóban lesz tárolva

> [!NOTE] 
> A titkosítatlan adattitkosítási kulcsot a rendszer a teljesítmény javítása érdekében egy rövid időre gyorsítótárazza a memóriában, és annak elteltével azonnal törli azt. Az állandó adathordozón a tárolás mindig a titkosítási főkulcs által titkosítva történik.

## <a name="key-rotation"></a>Kulcsrotálás

Felhasználó által kezelt kulcsok használata esetén az Azure Data Lake Store engedélyezi a titkosítási főkulcs rotálását. Az ADLS-fiók felhasználó által kezelt kulcsokkal történő beállítását lásd a [első lépések](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) oldalán.

### <a name="pre-requisites"></a>Előfeltételek

Az Azure Data Lake-fiók beállításakor az ügyfelek eldönthetik, hogy a saját kulcsaikat kívánják-e használni. A fiók létrehozása után ez a beállítás már nem módosítható. Az alapértelmezett titkosítási beállítások használatakor az adatok titkosítása mindig az Azure Data Lake által kezelt kulcsokkal történik. Ez esetben az ügyfélnek nincs lehetősége a kulcsok rotálására, mivel azokat az Azure Data Lake kezeli. Az alábbi lépések során azt feltételezzük, hogy felhasználó által kezelt (a Key Vaultból saját maga által kiválasztott) kulcsokat használ.

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Kulcsrotálás (titkosítási főkulcs esetén) az Azure Data Lake Store-ban

1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az Azure Data Lake Store-fiókhoz társított kulcsokat tároló Key Vaultot, és válassza a Kulcsok lehetőséget.

    ![Kulcsok](./media/data-lake-store-encryption/keyvault.png)

3.    Válassza ki az Azure Data Lake Store-fiókhoz társított kulcsot, és hozza létre annak egy új verzióját.
  
   Ekkor az Azure Data Lake kizárólag az új kulcsverzióra történő kulcsrotálást támogatja, a más kulcsra történő kulcsrotálás nem támogatott.

   ![új verzió](./media/data-lake-store-encryption/keynewversion.png)

4.    Nyissa meg az Azure Data Lake Storage-fiókot, és válassza a Titkosítás lehetőséget

    ![új verzió](./media/data-lake-store-encryption/select-encryption.png)

5.    A rendszer egy megjegyzést jelenít meg, amely arról tájékoztatja, hogy a kulcs új verziója érhető el, valamint megjelenik az új verzióra történő kulcsrotálásra szolgáló gomb. A kulcs új verzióra történő frissítéséhez kattintson a Kulcs rotálása lehetőségre.

    ![kész](./media/data-lake-store-encryption/rotatekey.png)

6. Ez a művelet kevesebb mint 2 percet vehet igénybe, és a kulcsrotálás nem jár várt leállással. A művelet befejezését követően a kulcs új verziója lesz használatban.

