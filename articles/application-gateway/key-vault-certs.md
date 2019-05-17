---
title: SSL-lezárást az Azure Key Vault tanúsítványokkal
description: Ismerje meg, hogyan integrálható az Azure Application Gateway a Key Vault használata a HTTPS használatára konfigurált figyelői csatolt kiszolgálói tanúsítványok.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827630"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>SSL-lezárást a Key Vault tanúsítványokkal

[Az Azure Key Vault](../key-vault/key-vault-whatis.md) a platform által felügyelt titkos kulcs tárolása, hogy a titkos kulcsok és SSL-tanúsítványok védelme érdekében használhatja. Az Azure Application Gateway támogatja a Key Vault integration (a nyilvános előzetes verzió) HTTPS használatára konfigurált figyelői csatolt kiszolgálói tanúsítványok. Ez a támogatás a v2 Termékváltozat Application Gateway-átjáró korlátozódik.

> [!IMPORTANT]
> Az Application Gateway a Key Vault-integráció jelenleg nyilvános előzetes verzióban érhető el. Ebben az előzetes verzióban egy szolgáltatásiszint-szerződés (SLA) nélkül, és nem ajánlottak éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A nyilvános előzetes verziója két modellel SSL-lezárást biztosít:

- Explicit módon megadhatja a figyelő csatolt SSL-tanúsítványok. Ebben a modellben a hagyományos módja az SSL-lezárások Application Gateway SSL-tanúsítványok átadása.
- Opcionálisan megadhat egy meglévő Key Vault-tanúsítvánnyal vagy titkos kód mutató hivatkozást, egy HTTPS használatára konfigurált figyelő létrehozásakor.

Átjáró integrációja a Key Vault kínál számos előnnyel jár, többek között:

- Nagyobb biztonság, mert az SSL-tanúsítványok közvetlenül nem kezeli az alkalmazásfejlesztési csoportnak. Integráció lehetővé teszi, hogy egy külön biztonsági csapat:
  * Állítsa be az application Gateway átjárók.
  * Szabályozhatja az application gateway-életciklusának.
  * Adja meg a kiválasztott application Gateway átjárók engedélyeket eléréséhez a key vaultban lévő tanúsítványt.
- Támogatja a meglévő tanúsítványok importálása a key vaultban. Vagy a Key Vault API-k segítségével létrehozhat, és új tanúsítványok kezelése a Key Vault megbízható partnerekkel.
- Az automatikus megújítását, a kulcstartóban tárolt tanúsítványok támogatása.

Az Application Gateway jelenleg csak a szoftverek ellenőrzött tanúsítványok támogatja. Hardveres biztonsági modul (HSM) – ellenőrzött tanúsítványok nem támogatottak. Az Application Gateway konfigurálása után a Key Vault-tanúsítványokat használ, a példányok lekéri a tanúsítványt a Key Vaultból, és az SSL-lezárást helyileg telepíteni őket. A példányok is elindítja a lekérdezést a Key Vault 24 órás időközönként beolvasni egy megújított verzióját a tanúsítványt, ha létezik. Ha egy frissített tanúsítványt talál, a HTTPS-figyelő jelenleg társított SSL-tanúsítvány automatikusan rotálásakor.

## <a name="how-integration-works"></a>Hogyan működik az integráció

Átjáró integrációja a Key Vault használata egy három lépéses konfigurációs folyamat van szükség:

1. **Felhasználó által hozzárendelt felügyelt identitás létrehozása**

   Hozzon létre, vagy újból felhasználhatja a meglévő felhasználó által hozzárendelt felügyelt identitást, mely az Application Gateway segítségével tanúsítványok lekérése a Key Vaultból az Ön nevében. További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md). Ez a lépés egy új eszközidentitást hoz létre az Azure Active Directory-bérlőben. Az identitás, amellyel az identitás létrehozása az előfizetés által megbízhatónak tartott.

1. **A key vault beállítása**

   Majd egy meglévő tanúsítvány importálása vagy hozzon létre egy új tárol a kulcstárolóban. A tanúsítvány az alkalmazásokat, amelyek haladjon végig az application gateway által használható. Ebben a lépésben egy jelszó nélküli, base 64 kódolású a PFX-fájlok tárolása a key vault titkos kulcsot is használhatja. A key vaultban lévő tanúsítványt típusú objektumok elérhető automatikus megújítását képesség miatt tanúsítványtípust használatát javasoljuk. Miután létrehozott egy tanúsítványt, vagy egy titkos kulcsot, hozzáférési szabályzatokat határozhat meg, hogy az identitás nyújtják a key vault *első* a titkos kulcsot a hozzáférést.

1. **Az application gateway konfigurálása**

   Miután elvégezte az előző két lépést, állítsa be, vagy módosíthatja egy meglévő application gateway a felhasználó által hozzárendelt felügyelt identitás használatára. Beállíthatja a HTTP-figyelő SSL-tanúsítvány mutasson a teljes URI-ját a Key Vault-tanúsítvánnyal vagy titkos kód azonosítója.

   ![A Key vault-tanúsítványok](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>További lépések

[SSL-lezárást konfigurálása a Key Vault tanúsítványokkal az Azure PowerShell-lel](configure-keyvault-ps.md)
