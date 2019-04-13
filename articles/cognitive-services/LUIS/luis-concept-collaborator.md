---
title: Együttműködés
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS-alkalmazások egyetlen tulajdonosnak és a választható közreműködők, így egyetlen alkalmazást hozzon létre több személy van szükség.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 34ae47723762215fdea99362419c3875dc2e5942
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522745"
---
# <a name="collaborating-with-other-authors"></a>A többi szerzőjének közreműködő

A LUIS-alkalmazások egyetlen tulajdonosnak és a választható közreműködők, így egyetlen alkalmazást hozzon létre több személy van szükség.

## <a name="luis-account"></a>A LUIS-fiók
A LUIS-fiókhoz társítva egyetlen [Microsoft Live](https://login.live.com/) fiókot. Minden egyes LUIS fiókot kap egy ingyenes [kulcs létrehozási](luis-concept-keys.md#authoring-key) való használatra a LUIS-alkalmazások készítése a fiók hozzáféréssel rendelkezik. 

A LUIS-fiók számos LUIS alkalmazás lehet.

Lásd: [Azure Active Directory-bérlő felhasználói](luis-how-to-collaborate.md#azure-active-directory-tenant-user) további információ az Active Directory felhasználói fiókokat. 

## <a name="luis-app-owner"></a>A LUIS alkalmazás tulajdonosa

A fiók, amely létrehoz egy alkalmazást a tulajdonosa, és minden alkalmazás rendelkezik egy egyetlen tulajdonosa. Az alkalmazás megjelenik a tulajdonos **[beállítások](luis-how-to-collaborate.md)** lapot. A tulajdonos törölheti az alkalmazást e-maileket kapni, amikor a végpont kvóta eléri 75 %-a havi korlátot. 

## <a name="authorization-roles"></a>Engedély szerepkörök
A LUIS tulajdonosok és a egy kivétellel közreműködők nem támogatja a különböző szerepköröket. Az egyetlen fiók, amely törölheti az alkalmazás tulajdonosa.

Ha érdekli a modellhez való hozzáférés szabályozása, fontolja meg, a modell szeletelés LUIS kisebb-alkalmazásokba, ahol minden egyes kisebb alkalmazás közreműködők korlátozott készletével rendelkezik. Használat [Dispatch](https://aka.ms/dispatch-tool) , hogy egy szülő LUIS-alkalmazás kezelése a szülő és gyermek alkalmazások működésének összehangolását.

## <a name="transfer-ownership"></a>Tulajdonjogok átadása
A LUIS tulajdon-átruházás, nem biztosít, azonban bármely közreműködő alkalmazást, és ezután importálja azt az alkalmazás létrehozása. Vegye figyelembe, hogy az új alkalmazás rendelkezik egy másik alkalmazás. Az alkalmazás új betanított, közzé kell, és a használt új végpont.

## <a name="luis-app-collaborators"></a>A LUIS alkalmazás közreműködők
Az alkalmazások tulajdonosai közreműködők is hozzáadhat egy alkalmazást. Adja hozzá a közreműködő e-mail címet az alkalmazás tulajdonosa kell  **[beállítások](luis-how-to-collaborate.md)**. A közreműködő, az alkalmazás teljes hozzáféréssel rendelkezik. A közreműködő törölheti az alkalmazást, ha az alkalmazás a közreműködőt fiókot eltávolítják, de a tulajdonos fiókja marad. 

Ha szeretné megosztani több alkalmazás közreműködőkkel együtt, a minden alkalmazás kell hozzá a közreműködő e-mail. 

## <a name="managing-multiple-authors"></a>Több szerző kezelése
A [LUIS](luis-reference-regions.md#luis-website) webhely jelenleg nem biztosít tranzakciószintű szerzői. Engedélyezheti a szerzők a kiindulási verzióról független verzióin működik. Az alábbiakban két különböző módszereket ismerteti.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Alkalmazáson belül több verziók kezelése
Első lépésként [Klónozás](luis-how-to-manage-versions.md#clone-a-version), a kiindulási verzióról, mindegyik szerző számára. 

Mindegyik Szerző módosítást hajt végre az alkalmazás a saját verzióját. Ha mindegyik Szerző elégedett a modellel, JSON-fájlok exportálása az új verziókat.  

Exportált alkalmazások olyan JSON-formátumú fájlokat, amelyek a módosítások hasonlítható. A fájlokat, és hozzon létre egy egyetlen JSON-fájlt az új verzió össze. Módosítsa a **versionId** tulajdonság a JSON az új, egyesített verzió jelölésére. Importálja azt a verziót az eredeti alkalmazásba. 

Ez a módszer lehetővé teszi, hogy egy aktív verzióját, egy szakasz és egy közzétett verziója. Összehasonlíthatja az eredményeket az interaktív vizsgálati panelen a három verziója között.

## <a name="manage-multiple-versions-as-apps"></a>Alkalmazások több verziók kezelése
[Exportálás](luis-how-to-manage-versions.md#export-version) Alapverzió. Mindegyik Szerző importálja a verziót. A személy, amely az alkalmazás importál a verzió tulajdonosa. Amikor végzett a verzió módosítása az alkalmazás exportálása. 

Exportált alkalmazások olyan JSON-formátumú fájlokat, amelyek a módosítások az alap exportálás összehasonlíthatók. A fájlokat, és hozzon létre egy egyetlen JSON-fájlt az új verzió össze. Módosítsa a **versionId** tulajdonság a JSON az új, egyesített verzió jelölésére. Importálja azt a verziót az eredeti alkalmazásba.

## <a name="collaborator-roles-vs-entity-roles"></a>A közreműködő szerepkört vs entitás szerepkörök

[Entitás szerepkörök](luis-concept-roles.md) a alkalmazni az adatmodellbe, a LUIS alkalmazás. A közreműködő szerepkört authoring hozzáférés szintjei érvényesek. 

## <a name="next-steps"></a>További lépések

Megismerheti [versioning](luis-concept-version.md) fogalmakat. 

Lásd: [Alkalmazásbeállítások](luis-how-to-collaborate.md) megtudhatja, hogyan kezelheti a LUIS-alkalmazás a közreműködők.

Lásd: [hozzáadása e-mail-hozzáférési listához](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) a jelentéskészítési API-kkal.
