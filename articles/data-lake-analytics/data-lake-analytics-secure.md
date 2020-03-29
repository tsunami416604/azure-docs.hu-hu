---
title: Biztonságos Azure Data Lake Analytics több felhasználó számára
description: Ismerje meg, hogyan konfigurálhat több felhasználót feladatok futtatására az Azure Data Lake Analytics szolgáltatásban.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813365"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Felhasználói hozzáférés konfigurálása a feladatadatokhoz az Azure Data Lake Analytics szolgáltatásban 

Az Azure Data Lake Analytics több felhasználói fiókkal vagy egyszerű szolgáltatásnévvel is futtathat feladatokat. 

Ahhoz, hogy ugyanezek a felhasználók láthassák a részletes feladatinformációkat, a felhasználóknak képesnek kell lenniük a feladatmappák tartalmának olvasására. A feladatmappák a `/system/` címtárban találhatók. 

Ha a szükséges engedélyek nincsenek konfigurálva, a felhasználó hibaüzenetet láthat:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Felhasználói hozzáférés konfigurálása a feladatadatokhoz

A Felhasználó **hozzáadása varázslóval** konfigurálhatja a mappákon lévő ACL-kat. További információt az Új felhasználó hozzáadása című témakörben [talál.](data-lake-analytics-manage-use-portal.md#add-a-new-user)

Ha részletesebb vezérlésre van szüksége, vagy parancsfájlt kell írnia, akkor biztosítsa a mappákat az alábbiak szerint:

1. Grant **execute** permissions (via an access ACL) on the root folder:
   - /
   
2. A feladatmappákat tartalmazó **mappákvégrehajtási** és **olvasási** engedélyeit (hozzáférési kulcson és alapértelmezett Hozzáférési ponton keresztül) engedélyezze. 2018. május 25-én egy adott feladathoz például ezekhez a mappákhoz hozzá kell férni:
   - /rendszer
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>További lépések
[Új felhasználó hozzáadása](data-lake-analytics-manage-use-portal.md#add-a-new-user)
