---
title: Az Azure Key Vault szabályozására vonatkozó útmutatás
description: A Key Vault szabályozása korlátozza az egyidejű hívások számát az erőforrások túlzott használatának megelőzése érdekében.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f32a988ec0d75ca8d8eca04e69edd7226bf283b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432085"
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vault szabályozására vonatkozó útmutatás

Szabályozás egy olyan folyamat, amely korlátozza az Azure-szolgáltatás egyidejű hívásainak számát az erőforrások túlzott használatának megakadályozása érdekében. Az Azure Key Vault (AKV) nagy mennyiségű kérelem kezelésére lett kialakítva. Ha túlnyomó számú kérelem történik, az ügyfél kéréseinek szabályozása segít fenntartani az AKV szolgáltatás optimális teljesítményét és megbízhatóságát.

A sávszélesség-szabályozási korlátok a forgatókönyvtől függően változnak. Ha például nagy mennyiségű írást hajt végre, a szabályozás lehetősége nagyobb, mint ha csak olvasásokat hajt végre.

## <a name="how-does-key-vault-handle-its-limits"></a>Hogyan kezeli a Key Vault a korlátait?

A Key Vault szolgáltatáskorlátai megakadályozzák az erőforrásokkal való visszaélést, és biztosítják a szolgáltatás minőségét a Key Vault összes ügyfele számára. A szolgáltatás küszöbértékének túllépése esetén a Key Vault egy adott időszakra korlátozza az adott ügyféltől érkező további kérelmeket, a 429-es HTTP-állapotkódot (túl sok kérést) adja vissza, és a kérés sikertelen lesz. Sikertelen kérelmek, amelyek 429-es számlálót akey vault által nyomon követett szabályozási korlátok között adnak vissza. 

A Key Vault eredetileg úgy lett kialakítva, hogy a titkos kulcsok tárolására és lekérésére szolgáljon az üzembe helyezés időpontjában.  A világ fejlődött, és a Key Vault használatos futásidőben tárolására és lekérésre titkos kulcsokat, és gyakran alkalmazások és szolgáltatások szeretné használni Key Vault, mint egy adatbázis.  A jelenlegi korlátok nem támogatják a magas átviteli sebességet.

A Key Vault eredetileg az [Azure Key Vault szolgáltatáskorlátaiban](service-limits.md)megadott korlátokkal jött létre.  A Key Vault beátviteli díjakon keresztüli maximalizálásához az alábbi ajánlott irányelvek és ajánlott eljárások az átviteli sebesség maximalizálására:
1. Győződjön meg róla, hogy a szabályozás a helyén van.  Az ügyfélnek tiszteletben kell tartania a 429-es évek exponenciális visszalépési irányelveit, és biztosítania kell, hogy az alábbi útmutatásnak megfelelően újrapróbálkozásokat végez.
1. Ossza fel a Key Vault-forgalmat több tároló és különböző régiók között.   Minden biztonsági/rendelkezésre állási tartományhoz használjon külön tárolót.   Ha öt alkalmazással rendelkezik, amelyek mindegyike két régióban található, akkor 10-et ajánlunk, amelyek mindegyike az alkalmazás és a régió egyedi titkos titkait tartalmazza.  Az összes tranzakciótípusra vonatkozó előfizetési szintű korlát az egyes kulcstartó-korlát ötszöröse. Például hsm-egyéb tranzakciók előfizetésenként legfeljebb 5000 tranzakció 10 másodperc előfizetésenként. Fontolja meg a titkos kulcsot a szolgáltatáson vagy az alkalmazáson belül, hogy az RPS-t közvetlenül a key vaultba csökkentse, és/vagy kezelje a burst alapú forgalmat.  A forgalom felosztása a különböző régiók között a késés minimalizálása érdekében, és egy másik előfizetés/tároló használata.  Ne küldjön több, mint az előfizetési korlátot a Key Vault szolgáltatás egyetlen Azure-régióban.
1. Gyorsítótárazhatja az Azure Key Vaultból beolvasott titkos kulcsokat a memóriában, és amikor csak lehetséges, újra felhasználhatja a memóriából.  Csak akkor olvassa be újra az Azure Key Vaultból, ha a gyorsítótárazott másolat nem működik (például azért, mert ellett forgatva a forrásnál). 
1. A Key Vault a saját szolgáltatási titkos kulcsokhoz készült.   Ha az ügyfelek titkos adatait tárolja (különösen a nagy átviteli sebességű kulcstárolási forgatókönyvek esetében), fontolja meg a kulcsok titkosítással való elhelyezését egy adatbázisban vagy tárfiókban, és csak a főkulcsot tárolja az Azure Key Vaultban.
1. Titkosítsa, csomagolja, és ellenőrizze a nyilvános kulcsú műveleteket lehet végrehajtani, nem férnek hozzá a Key Vault, amely nem csak csökkenti a szabályozás kockázatát, hanem javítja a megbízhatóságot (mindaddig, amíg megfelelően cache a nyilvános kulcsú anyag).
1. Ha a Key Vault használatával tárolja a hitelesítő adatokat egy szolgáltatás, ellenőrizze, hogy a szolgáltatás támogatja-e az Azure AD-hitelesítés közvetlen hitelesítéséhez. Ez csökkenti a Key Vault terhelését, javítja a megbízhatóságot és egyszerűsíti a kódot, mivel a Key Vault már használhatja az Azure AD-jogkivonatot.  Számos szolgáltatás költözött az Azure AD Auth használatával.  Tekintse meg az [Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)aktuális listáját.
1. Fontolja meg a terhelés/üzembe helyezés hosszabb ideig történő megtorpantását, hogy a jelenlegi RPS-korlátok alatt maradjon.
1. Ha az alkalmazás több csomópontot tartalmaz, amelyeknek ugyanazt a titkos kulcsot kell olvasniuk, akkor fontolja meg egy ventilátor-kiválasztó minta használatát, ahol az egyik entitás beolvassa a titkos kulcsot a Key Vaultból, és a rajongók at az összes csomópontnak.   A beolvasott titkokat csak a memóriában gyorsítótárazza.
Ha úgy találja, hogy a fenti még mindig nem felel meg az Ön igényeinek, kérjük, töltse ki az alábbi táblázatot, és lépjen kapcsolatba velünk, hogy meghatározzák, milyen további kapacitást lehet hozzáadni (például az alábbi szemléltető célokra csak).

| A trezor neve | Páncélterem régiója | Objektumtípusa (titkos kulcs, kulcs vagy tanúsítvány) | Művelet(ek)* | Kulcs típusa | Kulcs hossza vagy íve | HSM kulcs?| Állandósult állapotú RPS szükséges | Csúcs RPS szükséges |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Kulcs | Előjel | EC | P-256 | Nem | 200 | 1000 |

\*A lehetséges értékek teljes listáját az [Azure Key Vault-műveletek ben.](/rest/api/keyvault/key-operations)

Ha további kapacitást hagynak jóvá, kérjük, vegye figyelembe a kapacitásnövekedés eredményeként a következőket:
1. Az adatkonzisztenciamodell módosul. Miután egy tároló további átviteli kapacitással szerepel, a Key Vault-szolgáltatás adatkonzisztencia-garancia módosításai (nagyobb mennyiségű RPS teljesítéséhez szükséges, mivel az alapul szolgáló Azure Storage-szolgáltatás nem tud lépést tartani).  Dióhéjban:
  1. **Engedélyezése nélkül lista**: A Key Vault szolgáltatás tükrözi az eredményeket egy írási művelet (pl. SecretSet, CreateKey) azonnal a későbbi hívások (pl. SecretGet, KeySign).
  1. **Az engedélyezési lista:** A Key Vault szolgáltatás tükrözi az eredményeket egy írási művelet (pl. SecretSet, CreateKey) 60 másodpercen belül a későbbi hívások (pl. SecretGet, KeySign).
1. Az ügyfélkódnak 429 újrapróbálkozás esetén tiszteletben kell tartania a visszamaradási házirendet. A Key Vault szolgáltatást hívó ügyfélkód nem próbálkozhat azonnal újra a Key Vault-kérelmekkel, amikor 429-es válaszkódot kap.  Az Azure Key Vault szabályozására vonatkozó itt közzétett útmutatást azt javasolja, hogy exponenciális visszalépés alkalmazása esetén egy 429 Http-válaszkód.

Ha van egy érvényes üzleti esetében a magasabb fojtószelep korlátok, kérjük lépjen kapcsolatba velünk.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Az alkalmazás szabályozása a szolgáltatási korlátokra reagálva

A szolgáltatás szabályozása esetén az alábbi **ajánlott eljárásokat** kell végrehajtania:
- A kérelmekre jutó műveletek számának csökkentése.
- Csökkentse a kérések gyakoriságát.
- Kerülje az azonnali újrapróbálkozásokat. 
    - Minden kérelem a használati korlátokkal szemben halmozódik fel.

Az alkalmazás hibakezelésének megvalósításakor használja a 429-es HTTP-hibakódot az ügyféloldali szabályozás szükségességének észleléséhez. Ha a kérelem ismét sikertelen egy HTTP 429-es hibakóddal, továbbra is egy Azure-szolgáltatáskorláttal találkozik. Továbbra is használja az ajánlott ügyféloldali szabályozási módszert, és próbálja meg újra a kérelmet, amíg sikeres nem lesz.

Az exponenciális visszamaradást megvalósító kód az alábbiakban látható. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Ez a kód használata egy ügyfél C# alkalmazás egyszerű. 

### <a name="recommended-client-side-throttling-method"></a>Ajánlott ügyféloldali szabályozási módszer

A 429-es HTTP-hibakódesetén kezdje el az ügyfél szabályozását egy exponenciális visszautasítási megközelítés sel:

1. Várjon 1 másodpercet, próbálkozzon újra kéréssel
2. Ha még mindig 2 másodpercig van szabályozva, próbálkozzon újra a kéréssel
3. Ha még mindig 4 másodpercig van szabályozva, próbálkozzon újra a kéréssel
4. Ha továbbra is szabályozta, várjon 8 másodpercet, próbálkozzon újra a kéréssel
5. Ha továbbra is 16 másodpercig van szabályozva, próbálkozzon újra a kéréssel

Ezen a ponton nem kell http 429 válaszkódokat kapnia.

## <a name="see-also"></a>Lásd még

A szabályozás mélyebb tájolását a Microsoft Cloud ban a Szabályozási minta című témakörben [tetszési témakörben tájékozódhat.](https://docs.microsoft.com/azure/architecture/patterns/throttling)

