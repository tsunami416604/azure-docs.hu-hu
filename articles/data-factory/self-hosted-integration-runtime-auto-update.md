---
title: Saját üzemeltetésű integrációs modul automatikus frissítése és elévülési értesítés
description: További információ a saját üzemeltetésű integrációs modul automatikus frissítéséről és a lejárati értesítésekről
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 6a6c897d92d469fd6247dd51f2bacb91032ac123
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122437"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Saját üzemeltetésű integrációs modul automatikus frissítése és elévülési értesítés

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan engedélyezhető a saját üzemeltetésű integrációs modul automatikus frissítése a legújabb verzióra, és hogyan kezeli az ADF a saját üzemeltetésű integrációs modul verzióit.

## <a name="self-hosted-integration-runtime-auto-update"></a>Saját üzemeltetésű Integration Runtime automatikus frissítése
Ha a helyi gépen vagy egy Azure-beli virtuális gépen telepít egy saját üzemeltetésű integrációs modult, akkor két lehetőség közül választhat a saját üzemeltetésű integrációs modul verziójának kezeléséhez: automatikus frissítés vagy manuális karbantartás. Az ADF általában a saját üzemeltetésű integrációs modul két új verzióját bocsátja rendelkezésére minden hónapban, amely új funkció kiadását, hibajavítását vagy javítását tartalmazza. Ezért javasoljuk, hogy frissítsen a legújabb verzióra a legújabb funkció és a fejlesztés érdekében.

A legkényelmesebb módszer az automatikus frissítés engedélyezése a saját üzemeltetésű integrációs modul létrehozásakor vagy szerkesztésekor. Ezt követően a rendszer automatikusan frissíti a legújabb verziót. Azt is megteheti, hogy a frissítést a legmegfelelőbb időpontra ütemezze, ahogy szeretné.

![Automatikus frissítés engedélyezése](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Az utolsó frissítés DateTime értéket a saját üzemeltetésű Integration Runtime-ügyfélben tekintheti meg.

![Automatikus frissítés engedélyezése](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> A saját üzemeltetésű integrációs modul stabilitásának biztosítása érdekében, bár két verziót bocsátunk ki, a rendszer havonta egyszer automatikusan frissíti. Néha azt is tapasztalja, hogy az automatikusan frissített verzió a legújabb verzió korábbi verziója. Ha a legújabb verziót szeretné beszerezni, lépjen a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Saját üzemeltetésű Integration Runtime elévülési értesítés
Ha manuálisan szeretné szabályozni a saját üzemeltetésű integrációs modul verziószámát, letilthatja az automatikus frissítés beállítását, és manuálisan is telepítheti azt. A saját üzemeltetésű integrációs modul minden verziója egy év múlva lejár. A lejáró üzenet az ADF-Portálon és a saját üzemeltetésű Integration Runtime Client **90 nappal** a lejárat előtt jelenik meg.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [az Integration Runtime fogalmait a Azure Data Factoryban](./concepts-integration-runtime.md).

- Ismerje meg, hogyan [hozhat létre saját üzemeltetésű integrációs modult a Azure Portal](./create-self-hosted-integration-runtime.md).