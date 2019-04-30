---
title: Az Azure Data Lake Analytics biztonságos több felhasználó számára
description: Ismerje meg, hogyan konfigurálhatja a több felhasználó számára az Azure Data Lake Analytics-feladatok futtatása.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813365"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>A feladat adatainak feladatinformációkat felhasználói elérésének konfigurálása az Azure Data Lake Analytics 

Az Azure Data Lake Analytics segítségével több felhasználói fiókhoz vagy szolgáltatásnevek feladatok futtatásához. 

Ahhoz, hogy ezeket a feladat részletes információk megtekintéséhez ugyanazokat a felhasználókat a felhasználóknak kell el tudják olvasni a feladat mappák tartalmát. A feladat mappákban található `/system/` könyvtár. 

A szükséges engedélyek nincsenek konfigurálva, a felhasználó hiba jelenhet meg: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Feladat adatainak való felhasználói hozzáférés konfigurálása

Használhatja a **varázslót** a hozzáférés-vezérlési listák konfigurálása a mappák. További információkért lásd: [új felhasználó hozzáadása](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Ha többre van szüksége szabályozható, vagy a parancsfájl az engedélyeket kell majd a mappák biztonságossá a következő:

1. Engedélyezés **végrehajtása** engedélyeket (keresztül hozzáférési ACL-t), a legfelső szintű mappához:
   - /
   
2. Támogatás **végrehajtása** és **olvasása** engedélyeket (keresztül hozzáférési ACL-t és a egy alapértelmezett ACL-t) a feladat mappákat tartalmazó. Ha például egy adott feladat, a 2018. május 25., ezek a mappák kell érhető el:
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /System/jobservice/Jobs/Usql
   - /System/jobservice/Jobs/Usql/2018
   - /System/jobservice/Jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /System/jobservice/Jobs/Usql/2018/05/25/11
   - /System/jobservice/Jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>További lépések
[Új felhasználó hozzáadása](data-lake-analytics-manage-use-portal.md#add-a-new-user)
