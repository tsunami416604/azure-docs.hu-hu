---
title: Adatok exportálása vagy törlése - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Teljes mértékben kézben tarthatja az adatokat. Ebből a cikkből megtudhatja, hogyan tekintheti meg, exportálhatja vagy törölheti az adatokat a Custom Vision szolgáltatásban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718972"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Felhasználói adatok exportálása vagy törlése a Custom Vision programban

A Custom Vision felhasználói adatokat gyűjt a szolgáltatás működtetéséhez, de az ügyfelek teljes mértékben szabályozhatják az adataik megtekintését, exportálását és törlését a Custom Vision [Training API-k](https://go.microsoft.com/fwlink/?linkid=865446)használatával.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

A felhasználói adatok egyéni látással történő exportálásáról és törléséről az alábbi táblázatban olvashat.

| Adatok | Exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiókadatok (előfizetési kulcsok) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Törlés az Azure Portalon (Azure-előfizetések). Vagy a "Fiók törlése" gomb használata CustomVision.ai beállítások lapon (Microsoft-fiókelőfizetések) | 
| Iteráció részletei | [GetIteration (GetIteration)](https://go.microsoft.com/fwlink/?linkid=865446) | [Törlés](https://go.microsoft.com/fwlink/?linkid=865446) |
| Iterációs teljesítmény részletei | [GetIterationTeljesítmény](https://go.microsoft.com/fwlink/?linkid=865446) | [Törlés](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Ismétlések listája | [GetIterations (GetIterations)](https://go.microsoft.com/fwlink/?linkid=865446) | [Törlés](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projektek és projektrészletek | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) és [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [Project törlése](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Képcímkék | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) és [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [Tag törlése](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Képek | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (uri-t biztosít a kép letöltéséhez) és [a GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (uri-t biztosít a kép letöltéséhez) | [Képek törlése](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportált modellek | [GetExportok](https://go.microsoft.com/fwlink/?linkid=865446) | A fiók törlésekor törölve |
