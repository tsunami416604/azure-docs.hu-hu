---
title: SSL-lezárást a Key Vault tanúsítványokkal
description: Ismerje meg, hogyan integrálható az Azure application gateway a Key Vault használata a HTTPS-kompatibilis figyelői csatolt kiszolgálói tanúsítványok.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 9ab1a42578081d4e6537f221e3f3a8f4c7babde0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759613"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>SSL-lezárást a Key Vault tanúsítványokkal

[Az Azure Key Vault](../key-vault/key-vault-whatis.md) egy segítségével megakadályozhatja a titkos kulcsok és SSL-tanúsítványok titkos platform – felügyelt tár. Az Application Gateway támogatja a Key Vault integration (a nyilvános előzetes verzió) a HTTPS-kompatibilis figyelői csatolt kiszolgálói tanúsítványokat. Ez a támogatás a v2 Termékváltozat Application Gateway-átjáró korlátozódik.

> [!IMPORTANT]
> Az Application Gateway Key Vault-integráció jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A nyilvános előzetes verziója az SSL-lezárást a két modell áll:

- Explicit módon megadhatja a figyelő csatolt SSL-tanúsítványok. Ez az a hagyományos modelltől, az SSL-lezárások Application Gateway SSL-tanúsítványok való átadásához.
- Opcionálisan megadhat egy meglévő tanúsítványt a Key Vault hivatkozást, vagy titkos kulcs alatt HTTPS engedélyezve figyelő létrehozása.

Nincsenek számos előnnyel jár a Key Vault-integráció, többek között:

- Nagyobb biztonság, mivel az SSL-tanúsítványok sem közvetlenül kezeli a alkalmazásfejlesztési csoportnak. Key Vault-integráció lehetővé teszi, hogy egy külön biztonsági csapat üzembe helyezhető, életciklusának szabályozhatja, és válassza ki az Application Gateway átjárókon való hozzáférés tanúsítványokat a Key Vault szükséges engedélyt.
- Támogatja a meglévő tanúsítványok importálása a Key Vault, vagy használja a Key Vault API-k létrehozásához és kezeléséhez új tanúsítványokat a Key Vault megbízható partnerekkel.
- A Key Vault automatikusan megújítja a tanúsítványokat támogatása.

Application Gateway jelenleg csak a szoftverek ellenőrzött tanúsítványok támogatja. Hardveres biztonsági modul (HSM) érvényesítve tanúsítványok nem támogatottak. Az Application Gateway a Key Vault-tanúsítványok használatára van konfigurálva, ha a példányok lekéri a tanúsítványt a Key Vaultból, és az SSL-lezárást helyileg telepítse őket. A példányok Ha létezik is rendszeres időközönként lekérdezi a Key Vault egy 24 órás időközönként egy megújított verzióját a tanúsítvány lekéréséhez. Ha egy frissített tanúsítványt talál, a HTTPS-figyelő jelenleg társított SSL-tanúsítvány automatikusan rotálásakor.

## <a name="how-it-works"></a>Működés

A Key Vault-integráció egy három lépéses konfigurációs folyamat van szükség:

1. **A felhasználóhoz hozzárendelt felügyelt identitás létrehozása**

   Létre kell hoznia vagy újból felhasználhatja a hozzárendelt felügyelt identitás, amely tanúsítványok lekérése a Key Vaultból az Ön nevében használja az Application Gateway egy meglévő felhasználó. További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md) Ebben a lépésben az Azure AD-bérlőjéhez, amely az identitás létrehozásához használt előfizetés által megbízhatónak tartott egy új eszközidentitást hoz létre.
1. **Key Vault beállítása**

   Hozzon létre egy új tanúsítványt a Key Vault használatával az Application Gateway futó alkalmazások által használt vagy kell majd vagy importálása. A Key Vault titkos kulcsából, tárolt jelszó nélküli base 64 kódolású PFX-fájl is ebben a lépésben lehet használni. Egy tanúsítványtípust használata a Key Vaultban tanúsítvány típusú objektumok elérhető automatikus megújítását képességek miatt. Egy tanúsítvány vagy titkos kód létrehozása után hozzáférési szabályzatokat definiálni kell, hogy az identitás nyújtják a Key Vault *első* beolvasni a titkos kulcsot a hozzáférést.

1. **Application Gateway konfigurálása**

   Az előző két lépést követően kiépítése, vagy módosítsa a felhasználóhoz hozzárendelt felügyelt identitás használata meglévő Application Gateway. Is konfigurálhatja a HTTP-figyelő SSL-tanúsítvány átirányítása a teljes URI-t a Key Vault-tanúsítvány vagy titkos kód azonosítója.

![A Key Vault-tanúsítványok](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>További lépések

[SSL-lezárást konfigurálása az Azure PowerShell-lel a Key Vault tanúsítványokkal](configure-keyvault-ps.md).