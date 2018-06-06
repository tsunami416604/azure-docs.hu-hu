---
title: Azure Data Lake Analytics biztonságos több felhasználó számára
description: Ismerje meg, hogy több felhasználó feladatok futtatása az Azure Data Lake Analytics konfigurálása.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 1d92e6d0e619584dedcbc9a66450c25dd1ac8b75
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701398"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Feladat információinak feladat azon felhasználói elérésének konfigurálása az Azure Data Lake Analytics 

Az Azure Data Lake Analytics segítségével több felhasználói fiókhoz vagy szolgáltatásnevekről feladatok futtatásához. 

Ahhoz, hogy ezek a feladatok részletes adatainak megtekintéséhez ugyanazokat a felhasználókat a felhasználóknak kell el tudják olvasni a feladat mappák tartalmát. A feladat mappák találhatók `/system/` könyvtár. 

Ha a szükséges engedélyekkel vannak beállítva, a felhasználó hiba jelenhetnek meg: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>A feladat információi a felhasználói hozzáférés konfigurálása

Használhatja a **varázslót** az ACL-ek konfigurálása a mappák. További információkért lásd: [új felhasználó hozzáadásához](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Ha többet szeretne a tanúsítványhasználat pontos szabályzása, és nem szükséges parancsfájl az engedélyeket, majd a mappák biztonságossá az alábbiak szerint:

1. Támogatás **hajtható végre** engedélyeket (keresztül hozzáférjen ACL) a gyökérmappához:
   - /
   
2. Támogatás **hajtható végre** és **olvasási** feladat mappákat tartalmazó engedélyeinek (keresztül hozzáférjen ACL és egy alapértelmezett hozzáférés-vezérlési lista). Például egy adott feladat futott a 2018. május 25. Ezek a mappák kell érhető el:
   - vagy
   - / rendszer/jobservice
   - /System/jobservice/Jobs
   - /System/jobservice/Jobs/Usql
   - /System/jobservice/Jobs/Usql/2018
   - /System/jobservice/Jobs/Usql/2018/05
   - /System/jobservice/Jobs/Usql/2018/05/25
   - /System/jobservice/Jobs/Usql/2018/05/25/11
   - /System/jobservice/Jobs/Usql/2018/05/25/11/01
   - rendszer/jobservice/feladatok/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>További lépések
[Új felhasználó hozzáadása](data-lake-analytics-manage-use-portal.md#add-a-new-user)
