---
title: Az Azure App Service Web Apps Java-alkalmazás hozzáadása
description: Ez az oktatóanyag bemutatja, hogyan lap vagy az Azure App Service Web Apps, amely már használatára van konfigurálva a Java-példány alkalmazás hozzáadása.
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: 71009370282fcfbd71c00b30d4ea22802035714d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Az Azure App Service Web Apps Java-alkalmazás hozzáadása
Amennyiben Ön inicializálta a Java-webalkalmazás a [Azure App Service] [ Azure App Service] dokumentált [Java-webalkalmazás létrehozása az Azure App Service](app-service-web-get-started-java.md), feltöltheti az alkalmazást úgy, hogy az a WAR a **webapps** mappa.

A navigációs elérési útját a **webapps** mappa alapján hogyan lehet beállítani a Web Apps példány eltér.

* Ha az Azure piactéren elérési útját használatával állítsa be a webalkalmazás a **webapps** mappa van a képernyőn **d:\home\site\wwwroot\bin\application\_server\webapps**, ahol **alkalmazás\_server** van érvényben a kiszolgáló nevét a Web Apps példányát. 
* Ha az Azure-alapú konfigurációs UI, az elérési útját használatával állítsa be a webalkalmazás a **webapps** mappa van a képernyőn **d:\home\site\wwwroot\webapps**. 

Megjegyzés: használható verziókezelő töltse fel az alkalmazás vagy a weblapokat, beleértve a [folyamatos integrációt](app-service-continuous-deployment.md). FTP egyben a beállítás a az alkalmazás vagy a weblapok; feltöltése FTP keresztül az alkalmazások telepítésével kapcsolatos további információkért lásd: [alkalmazás telepítése az FTP használatával](app-service-deploy-ftp.md).

Webalkalmazások Tomcat Megjegyzés: Miután a WAR-fájlt, a feltöltött a **webapps** mappa, a Tomcat alkalmazáskiszolgáló észleli, hogy hozzáadását, és automatikusan betölti azt. Vegye figyelembe, hogy a gyökérkönyvtárba másolt fájlok (eltérő WAR-fájl), a kiszolgáló kell újra kell indítani azokat a fájlokat használja. A Tomcat Java-webalkalmazások Azure-on futó autoload funkcionalitása alapján hozzáadott új WAR-fájlt, vagy új fájlok vagy könyvtárak hozzáadni a **webapps** mappa. 

<a name="see-also"></a>

## <a name="see-also"></a>Lásd még:
Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ].

[Application-insights-App-insights-Java-Get-Started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
