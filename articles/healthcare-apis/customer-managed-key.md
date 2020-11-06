---
title: Ügyfél által felügyelt kulcsok konfigurálása a FHIR készült Azure API-hoz
description: A FHIR Azure API-n keresztül a saját kulcs funkcióját is támogatja a Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 05c208ba3c9005d38b8924037748764f8d112e3a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398181"
---
# <a name="configure-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok konfigurálása

Amikor új Azure API-t hoz létre a FHIR-fiókhoz, a rendszer alapértelmezés szerint a Microsoft által felügyelt kulcsokkal titkosítja adatait. Most hozzáadhat egy második titkosítási réteget az adataihoz saját maga által választott és felügyelt kulcs használatával.

Az Azure-ban ez általában az ügyfél Azure Key Vaultjában (AKV) található titkosítási kulcs használatával valósítható meg. Az Azure SQL, az Azure Storage és a Cosmos DB néhány példa erre a képességre. A FHIR készült Azure API a Cosmos DB által nyújtott támogatást használja. Fiók létrehozásakor lehetősége van megadnia a AKV kulcs URI azonosítóját. Ezt a kulcsot a rendszer átadja a Cosmos DB az adatbázis-fiók üzembe helyezésekor. FHIR-kérés esetén Cosmos DB lekéri a kulcsot, és a használatával titkosítja vagy visszafejti az adatait. Az első lépésekhez tekintse át az alábbi hivatkozásokat:

- [Az Azure-előfizetéshez tartozó Azure Cosmos DB erőforrás-szolgáltató regisztrálása](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [A AKV-példány konfigurálása](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
-  [Hozzáférési szabályzat hozzáadása a AKV-példányhoz](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Kulcs létrehozása a AKV-ben](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

Miután létrehozta az Azure API-t a FHIR-fiókhoz Azure Portalon, a "További beállítások" lapon a "Database Settings" (adatbázis-beállítások) alatt "adattitkosítási" konfigurációs beállítás látható. Alapértelmezés szerint a szolgáltatás által felügyelt kulcs beállítás lesz kiválasztva. Itt megadhatja a AKV kulcsot az "ügyfél által felügyelt kulcs" lehetőség kiválasztásával. Itt megadhatja a másolt kulcs URI-JÁT.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API létrehozása a FHIR-hez":::

Vagy a kulcstartóból is kiválaszthatja a kulcsot:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Kiválasztó":::

A meglévő FHIR-fiókok esetében az alábbi módon tekintheti meg a kulcs titkosítási lehetőségeit (szolgáltatás vagy ügyfél által felügyelt kulcs) az "adatbázis" panelen. A konfigurációs beállítás kiválasztása után nem módosítható. A kulcsot azonban módosíthatja és frissítheti.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Adatbázis":::

Emellett a megadott kulcs új verzióját is létrehozhatja, amely után az adatok a szolgáltatás megszakítása nélkül lesznek titkosítva az új verzióval. A kulcshoz való hozzáférés eltávolításával is eltávolíthatja az adathozzáférést.