---
title: A LUIS alkalmazás együttműködés – beszédfelismerés
titleSuffix: Azure Cognitive Services
description: A LUIS-alkalmazások egyetlen tulajdonosnak és a választható közreműködők van szükség.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5573bd36761fb56f0731f886bd705327625f06fe
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734505"
---
# <a name="collaborating"></a>Együttműködés

A LUIS biztosít az együttműködést, hogy a csoport, hozzon létre egy alkalmazást.

## <a name="luis-account"></a>A LUIS-fiók
A LUIS-fiókhoz társítva egyetlen [Microsoft Live](https://login.live.com/) fiókot. Minden egyes LUIS fiókot kap egy ingyenes [kulcs létrehozási](luis-concept-keys.md#authoring-key) való használatra a LUIS-alkalmazások készítése a fiók hozzáféréssel rendelkezik. 

A LUIS-fiók számos LUIS alkalmazás lehet.

Lásd: [Azure Active Directory-bérlő felhasználói](luis-how-to-collaborate.md#azure-active-directory-tenant-user) további információ az Active Directory felhasználói fiókokat. 

## <a name="luis-app-owner"></a>A LUIS alkalmazás tulajdonosa
A fiók, amely létrehoz egy alkalmazást a tulajdonosa. Minden alkalmazás rendelkezik egy egyetlen tulajdonosa. A tulajdonos szerepel az alkalmazás  **[beállítások](luis-how-to-collaborate.md)**. Ez az a fiók, amely az alkalmazás törlése. Ez akkor is a fiókot, amely e-mailt kap, ha a végpont kvóta eléri 75 %-a havi korlátot. 

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

## <a name="next-steps"></a>További lépések

Megismerheti [versioning](luis-concept-version.md) fogalmakat. 

Lásd: [Alkalmazásbeállítások](luis-how-to-collaborate.md) megtudhatja, hogyan kezelheti a LUIS-alkalmazás a közreműködők.

Lásd: [hozzáadása e-mail-hozzáférési listához](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) a jelentéskészítési API-kkal.