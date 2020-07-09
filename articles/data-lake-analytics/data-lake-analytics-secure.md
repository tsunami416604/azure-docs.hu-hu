---
title: Biztonságos Azure Data Lake Analytics több felhasználó számára
description: Megtudhatja, hogyan konfigurálhat több felhasználót a feladatok futtatásához Azure Data Lake Analyticsban.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: how-to
ms.date: 05/30/2018
ms.openlocfilehash: 758bf7a41218bcb8f34761db6cbbd541b97b6a86
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120874"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>A feladatok adataihoz való felhasználói hozzáférés konfigurálása Azure Data Lake Analytics 

Azure Data Lake Analytics több felhasználói fiókot vagy egyszerű szolgáltatást is használhat a feladatok futtatásához. 

Ahhoz, hogy ugyanazok a felhasználók lássák a részletes feladatokat, a felhasználóknak el kell tudniuk olvasni a feladatok mappáinak tartalmát. A feladatok mappái a `/system/` címtárban találhatók. 

Ha a szükséges engedélyek nincsenek konfigurálva, a felhasználó a következő hibaüzenetet kaphatja:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Felhasználói hozzáférés beállítása a feladatokhoz

A **felhasználók hozzáadása varázslóval** konfigurálhatja a mappák ACL-jeit. További információ: [új felhasználó hozzáadása](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Ha részletesebb szabályozásra van szüksége, vagy az engedélyek parancsfájlját kell megadnia, akkor a mappák védelmét a következőképpen teheti meg:

1. **Végrehajtási** engedélyek megadása (hozzáférési ACL használatával) a gyökérkönyvtárban:
   - /
   
2. Adja meg a feladatok mappáit tartalmazó mappákon a **végrehajtási** és **olvasási** engedélyeket (hozzáférési ACL és alapértelmezett ACL-ek használatával). Például a 2018-es május 25-én futó adott feladatokhoz a következő mappákat kell elérni:
   - /System
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Következő lépések
[Új felhasználó hozzáadása](data-lake-analytics-manage-use-portal.md#add-a-new-user)
