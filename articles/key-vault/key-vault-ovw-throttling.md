---
title: Az Azure Key Vault szabályozási útmutatója
description: Key Vault-szabályozás korlátozza, hogy az erőforrások túlzott párhuzamos hívások száma.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c4923e86f8678458d6301503043413fb8a5629b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197368"
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vault szabályozási útmutatója

Szabályozás az kezdeményez, amely korlátozza az erőforrások túlzott elkerülése érdekében az Azure-szolgáltatás egyidejű beérkező hívásainak számát. Az Azure Key Vault (AKV) célja nagy mennyiségű kérelmet kezelni. Ha túlságosan sok kérelem történik, az ügyfél kérelmek szabályozása segítségével optimális teljesítményének és megbízhatóságának az AKV szolgáltatás karbantartása.

A forgatókönyv szabályozási korlátok függ. Például akkor, ha nagy mennyiségű írást, a szabályozás lehetősége magasabb, mint ha csak végez olvasási.

## <a name="how-does-key-vault-handle-its-limits"></a>Hogyan kezeli a Key Vault a teljesítménye korlátait?

A szolgáltatás korlátai a Key Vault meggátolják az erőforrások felhasználását, és biztosítják az összes Key Vault ügyfelének minőségi szolgáltatását. Ha túllépi a szolgáltatás küszöbértékét, Key Vault korlátozza az ügyféltől érkező további kérelmeket egy adott időtartamra, a 429-as HTTP-állapotkódot (túl sok kérés) adja vissza, és a kérés meghiúsul. Sikertelen kérelmek, amelyek a 429-es számú korlátozást adnak vissza a Key Vault által követett szabályozási korlátok felé. 

A Key Vault eredetileg a titkok tárolására és lekérésére szolgáltak a központi telepítés ideje alatt.  A világ fejlődött, és a Key Vault futtatáskor használatban van a titkok tárolására és beolvasására, és gyakran az alkalmazások és a szolgáltatások használni szeretnék a Key Vaultt, például az adatbázist.  Az aktuális határértékek nem támogatják a nagy átviteli sebességet.

A Key Vault eredetileg a [Azure Key Vault szolgáltatás korlátaiban](key-vault-service-limits.md)megadott korlátozásokkal lett létrehozva.  Ahhoz, hogy a lehető leghatékonyabban Key Vault a saját átviteli sebességét, az alábbi ajánlott irányelvek és ajánlott eljárások a teljesítmény maximalizálása érdekében:
1. Győződjön meg arról, hogy a szabályozás érvényben van.  Az ügyfélnek meg kell felelnie a 429-re vonatkozó exponenciális visszatartási szabályzatoknak, és gondoskodnia kell arról, hogy az alábbi útmutatás szerint próbálkozzon újra.
1. Ossza meg Key Vault forgalmát több tároló és különböző régió között.   Használjon külön tárolót minden egyes biztonsági/rendelkezésre állási tartományhoz.   Ha öt alkalmazással rendelkezik, amelyek mindegyike két régióban található, javasoljuk, hogy az alkalmazás és a régió számára egyedi titkokat tartalmazó 10 tárolót.  Az összes tranzakciótípus esetében az előfizetésre vonatkozó korlát ötszöröse az egyes kulcstartók maximális száma. Például HSM – az egyes előfizetésekhez tartozó egyéb tranzakciók 10 másodpercen belül 5 000 tranzakcióra korlátozódnak. Érdemes lehet gyorsítótárba venni a titkos kulcsot a szolgáltatáson vagy az alkalmazáson belül, hogy közvetlenül a Key vaultra csökkentse az RPS-t és/vagy a burst-alapú forgalmat.  A forgalmat a különböző régiók között is feloszthatja, hogy csökkentse a késést, és használjon egy másik előfizetést/tárat.  Ne küldjön egynél több előfizetési korlátot a Key Vault szolgáltatásnak egyetlen Azure-régióban.
1. Gyorsítótárazza a Azure Key Vault memóriából beolvasott titkokat, és amikor csak lehetséges, a memóriából újra felhasználhatja őket.  Azure Key Vault csak akkor olvassa újra a rendszer, ha a gyorsítótárazott másolat leáll (például azért, mert az a forrásnál van elforgatva). 
1. Key Vault a saját szolgáltatásainak titkára lett tervezve.   Ha az ügyfelek titkos kulcsait (különösen a nagy átviteli sebességű kulcsok tárolási forgatókönyvei esetében) tárolja, fontolja meg a kulcsok egy adatbázisban vagy a Storage-fiókban való elhelyezését titkosítással, és csak a főkulcsot tárolja Azure Key Vaultban.
1. A nyilvános kulcsú műveletek titkosítása, becsomagolása és ellenőrzése a Key Vaulthoz való hozzáférés nélkül végezhető el, ami nemcsak csökkenti a szabályozás kockázatát, hanem javítja a megbízhatóságot is (feltéve, hogy a nyilvános kulcsú anyagokat megfelelően gyorsítótárazza).
1. Ha a Key Vault segítségével tárolja a szolgáltatás hitelesítő adatait, ellenőrizze, hogy a szolgáltatás támogatja-e a HRE hitelesítést közvetlenül a hitelesítéshez. Ez csökkenti a Key Vault terhelését, javítja a megbízhatóságot, és leegyszerűsíti a kódot, mivel Key Vault mostantól az HRE tokent is használhatja.  Számos szolgáltatás átkerült a HRE-hitelesítés használatára.  Tekintse [meg az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)aktuális listáját.
1. Ügyeljen arra, hogy a terhelést/üzembe helyezést hosszabb idő alatt, a jelenlegi RPS-korlátokon belül is el lehessen osztani.
1. Ha az alkalmazás több olyan csomópontot tartalmaz, amelyeknek ugyanazokat a titkos kulcsokat kell beolvasniuk, akkor érdemes kipróbálni a mintázatot, ahol az egyik entitás beolvassa a titkos kulcsot a Key Vaultból, és kiveszi a rajongókat az összes csomópontra.   Csak a memóriában gyorsítótárazza a beolvasott titkokat.
Ha úgy látja, hogy a fentiek továbbra sem felelnek meg az igényeinek, töltse ki az alábbi táblázatot, és vegye fel velünk a kapcsolatot, és határozza meg, hogy milyen további kapacitás vehető fel (példa csak szemléltető célokra).

| Tár neve | Tár régiója | Objektumtípus (titok, kulcs vagy tanúsítvány) | Művelet (ek) * | Kulcs típusa | Kulcs hossza vagy görbéje | HSM-kulcs?| Stabil állapot RPS szükséges | Az RPS-csúcs szükséges |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Kulcs | Jel | EC | P-256 | Nem | 200 | 1000 |

\* a lehetséges értékek teljes listáját lásd: [Azure Key Vault műveletek](/rest/api/keyvault/key-operations).

Ha további kapacitást hagy jóvá, vegye figyelembe a következőket a kapacitás növekedésének eredményeképpen:
1. Az adatkonzisztencia-modell módosul. Miután a tár engedélyezve van a további adatátviteli kapacitással, a Key Vault szolgáltatás adatkonzisztencia-garanciája módosul (a nagyobb mennyiségű RPS-vel való megfeleléshez szükséges, mivel a mögöttes Azure Storage szolgáltatás nem tud lépést tartani).  Dióhéjban:
  1. Az **engedélyezési lista nélkül**: a Key Vault szolgáltatás egy írási művelet eredményét fogja tükrözni (például SecretSet, CreateKey) azonnal a következő hívásokban (például: SecretGet, előjel).
  1. Az **engedélyezési listával**: a Key Vault szolgáltatás egy írási művelet eredményét fogja tükrözni (például SecretSet, CreateKey) a következő hívásokban (például:) 60 másodpercen belül. SecretGet, előjel).
1. Az ügyfél kódjának vissza kell térnie a 429-re vonatkozó újrapróbálkozási szabályzatra. A Key Vault szolgáltatást hívó ügyfélalkalmazás nem tudja azonnal újrapróbálkozni Key Vault kérések esetén, amikor 429-as kódú választ kap.  Az itt közzétett Azure Key Vault-szabályozási útmutató az exponenciális leállítási alkalmazását javasolja a 429 http-válasz kódjának fogadásakor.

Ha rendelkezik egy újabb szabályozási korlátairól üzleti esetet, lépjen kapcsolatba velünk a következő címen.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Az alkalmazás szolgáltatásokra vonatkozó korlátozások válaszul szabályozásának megtervezéséhez

A szolgáltatás szabályozásakor a következő **ajánlott eljárásokat** kell megvalósítani:
- Műveletek kérelmenkénti számának csökkentése.
- Csökkentse a kérelmek gyakoriságát.
- Azonnali újrapróbálkozás elkerülése érdekében. 
    - Összes kérelem lépheti túl a használati korlátok alapján.

Az alkalmazás hibakezelési megvalósításának, a 429-es HTTP-hibakód használatával észleli a ügyféloldali szabályozás. A kérelem újra HTTP 429-es hibaüzenettel meghiúsul, ha csökkenti az Azure-szolgáltatás továbbra is merültek fel. Továbbra is használhatja az ajánlott ügyféloldali metódus szabályozás, amíg be nem fejeződik, a kérelem újbóli megkísérlése.

Exponenciális visszatartással megvalósító kódot alább látható. 
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


A kód használata egy ügyfélalkalmazás C# esetében egyszerű. 

### <a name="recommended-client-side-throttling-method"></a>Ajánlott az ügyféloldali szabályozási módszer

HTTP 429-es hibakód, a szabályozás, az ügyfél egy exponenciális visszatartással megközelítéssel kezdete:

1. Várjon 1 másodperc, próbálkozzon újra a kérelmet
2. Ha továbbra is szabályozott várjon 2 másodperc, ismételje meg a kérelmet
3. Ha továbbra is szabályozott várakozási 4 másodperc, ismételje meg a kérelmet
4. Ha továbbra is szabályozott várakozási 8 másodperc, ismételje meg a kérelmet
5. Ha továbbra is szabályozott várakozási 16 másodperc, ismételje meg a kérelmet

Ezen a ponton meg kell nem lehet kezdeti HTTP 429-es válaszkódot.

## <a name="see-also"></a>Lásd még

A Microsoft Cloud szabályozásának mélyebb tájolását lásd: [szabályozási minta](https://docs.microsoft.com/azure/architecture/patterns/throttling).

