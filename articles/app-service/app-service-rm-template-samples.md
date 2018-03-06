---
title: "Azure Resource Manager-sablonminták – App Service | Microsoft Docs"
description: "Azure Resource Manager-sablonminták – App Service"
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Azure Resource Manager-sablonok az Azure Web Appshez

A következő táblázat Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz. A webalkalmazás-sablonok létrehozásakor előforduló gyakori hibák elkerülése érdekében olvassa el a [webalkalmazások Azure Resource Manager-sablonokkal való üzembehelyezési útmutatójában](web-sites-rm-template-guidance.md) foglalt javaslatokat.

| | |
|-|-|
|**Webalkalmazás üzembe helyezése**||
| [Egy GitHub-adattárhoz kapcsolódó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Üzembe helyez egy az Azure-webalkalmazást, amely a GitHubról kér le kódokat. |
| [Egyéni üzembehelyezési pontokat használó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Üzembe helyez egy Azure-webalkalmazást egyéni üzembehelyezési pontok/környezetek használatával. |
|**Webalkalmazás konfigurálása**||
| [Webalkalmazás-tanúsítvány a Key Vaultból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Üzembe helyez egy Azure-webalkalmazáshoz tartozó tanúsítványt a Key Vault titkos kulcsából, és felhasználja egy SSL-kötés létrehozásához. |
| [Egyéni tartományt használó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Üzembe helyez egy Azure-webalkalmazást, amely egyéni gazdagépnévvel rendelkezik. |
| [Egyéni tartományt és SSL-t használó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Üzembe helyez egy Azure-webalkalmazást, amely egyéni gazdagépnévvel rendelkezik, és lekér egy webalkalmazás-tanúsítványt a Key Vaultból egy SSL-kötés létrehozásához. |
| [Webalkalmazás GoLang bővítménnyel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Üzembe helyez egy Azure-webalkalmazást a Golang helybővítménnyel, amely lehetővé teszi a Golangben fejlesztett webalkalmazások futtatását az Azure-ban. |
| [Webalkalmazás Java 8- és Tomcat 8-támogatással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Üzembe helyez egy Azure-webalkalmazást, amelyben engedélyezve van a Java 8 és a Tomcat 8, ezáltal lehetővé teszi a Java-alkalmazások futtatását az Azure-ban. |
|**Linux-webalkalmazás**||
| [Webalkalmazás Linux rendszeren és MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Üzembe helyez egy Azure-webalkalmazást Linux rendszeren, valamint egy Azure-adatbázist a MySQL használatához. |
| [Webalkalmazás Linux rendszeren és PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Üzembe helyez egy Azure-webalkalmazást Linux rendszeren, valamint egy Azure-adatbázist a PostgreSQL használatához. |
|**Webalkalmazás csatlakoztatott erőforrásokkal**||
| [Webalkalmazás és MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Üzembe helyez egy Azure-webalkalmazást Windows rendszeren, valamint egy Azure-adatbázist a MySQL használatához. |
| [Webalkalmazás és PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Üzembe helyez egy Azure-webalkalmazást Windows rendszeren, valamint egy Azure-adatbázist a PostgreSQL használatához. |
| [Webalkalmazás SQL Database-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Üzembe helyez egy Azure-webalkalmazást és egy alapszintű szolgáltatási szintű SQL Database-adatbázist. |
| [Webalkalmazás Blob Storage-kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Üzembe helyez egy Azure-webalkalmazást és egy blobtárolóhoz tartozó kapcsolati karakterláncot, amely lehetővé teszi az Azure Blob Storage használatát a webalkalmazásból. |
| [Webalkalmazás Redis Cache-gyorsítótárral](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Üzembe helyez egy Azure-webalkalmazást, amely rendelkezik egy Redis Cache-gyorsítótárral. |
|**App Service Environment**||
| [App Service Environment v2-környezet létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton. |
| [App Service Environment v2-környezet létrehozása ILB-címmel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton egy privát belső terheléselosztó címmel. |
| [Az alapértelmezett SSL-tanúsítvány konfigurálása egy ILB ASE vagy ILB ASE v2-környezet használatához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Egy ILB App Service-környezet vagy egy ILB App Service Environment v2-környezet használatához konfigurálja az alapértelmezett SSL-tanúsítványt. |
| | |
