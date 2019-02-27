---
title: Alkalmazások konfigurálása az Azure segítségével folyamatos integrációt és teljesítést folyamat integrálása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy konfigurációs fájl adatokat használó Azure-alkalmazások konfigurálása során a folyamatos integrációt és teljesítést
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 0e6b24175ffa28b2a0f361bc46fd6c41e09cae72
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885437"
---
# <a name="integrate-with-a-cicd-pipeline"></a>A CI/CD-folyamat integrálása

Az alkalmazás nem tudja elérni az Azure-alkalmazások konfigurálása a távoli lehetőségét szembeni rugalmasságát javítása érdekében egy fájlba, amelyet az alkalmazással együtt, és helyben betöltött az indítás során kell csomag aktuális konfigurációs adatok . Ez a megközelítés biztosítja, hogy az alkalmazás legalább lesz alapértelmezett beállítás értékeit. Ezek az értékek felülírja az alkalmazás a konfigurációs adattárolónál az újabb módosításokat elérhetővé válik.

## <a name="automate-configuration-data-retrieval"></a>Automatizálhatja a konfigurációs adatok beolvasása

Használatával a [ **exportálása** ](./howto-import-export-data.md#export-data) függvényt az Azure-alkalmazások konfigurálása, automatizálhatja egyetlen fájlként aktuális konfigurációs adatok lekérését jelenti. Ezt a fájlt majd Felvétel a build és a központi telepítési lépés a folyamatos integráció és folyamatos üzembe helyezés.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: ASP.NET-webalkalmazás létrehozása](quickstart-aspnet-core-app.md)  
