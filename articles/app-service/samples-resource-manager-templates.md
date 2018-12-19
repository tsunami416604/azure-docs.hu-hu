---
title: Azure Resource Manager-sablonminták – App Service | Microsoft Docs
description: Azure Resource Manager-sablonminták az App Service Web Apps szolgáltatásához
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 10/15/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 2c717d42f4461816fca2a48701d135c3126880f2
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584906"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service-ben az Azure Resource Manager-sablonokkal

A következő táblázat az Azure App Service Web Apps szolgáltatásának Azure Resource Manager-sablonjaira mutató hivatkozásokat tartalmaz. A webalkalmazás-sablonok létrehozásakor előforduló gyakori hibák elkerülése érdekében olvassa el a [webalkalmazások Azure Resource Manager-sablonokkal való üzembehelyezési útmutatójában](web-sites-rm-template-guidance.md) foglalt javaslatokat.

| | |
|-|-|
|**Webalkalmazás üzembe helyezése**||
| [App Service-csomag és alapszintű Linux-webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Egy Linuxhoz konfigurált Azure-webalkalmazást helyez üzembe. |
| [App Service-csomag és alapszintű Windows-webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Egy Windowshoz konfigurált Azure-webalkalmazást helyez üzembe. |
| [Egy GitHub-adattárhoz kapcsolódó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Üzembe helyez egy az Azure-webalkalmazást, amely a GitHubról kér le kódokat. |
| [Egyéni üzembehelyezési pontokat használó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Üzembe helyez egy Azure-webalkalmazást egyéni üzembehelyezési pontok/környezetek használatával. |
|**Webalkalmazás konfigurálása**||
| [Webalkalmazás-tanúsítvány a Key Vaultból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Üzembe helyez egy Azure-webalkalmazáshoz tartozó tanúsítványt az Azure Key Vault titkos kulcsából, és felhasználja egy SSL-kötés létrehozásához. |
| [Egyéni tartományt használó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Üzembe helyez egy Azure-webalkalmazást, amely egyéni gazdagépnévvel rendelkezik. |
| [Egyéni tartományt és SSL-t használó webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Üzembe helyez egy Azure-webalkalmazást, amely egyéni gazdagépnévvel rendelkezik, és lekér egy webalkalmazás-tanúsítványt a Key Vaultból egy SSL-kötés létrehozásához. |
| [Webalkalmazás GoLang bővítménnyel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Üzembe helyez egy Azure-webalkalmazást a Golang webhelybővítménnyel. Ezután futtathatja a Golangben fejlesztett webalkalmazásokat az Azure-ban. |
| [Webalkalmazás Java 8- és Tomcat 8-támogatással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Java 8- és Tomcat 8-támogatással rendelkező Azure webalkalmazást helyez üzembe. Ezután Java-alkalmazásokat futtathat az Azure-ban. |
|**Linux-webalkalmazás csatlakoztatott erőforrásokkal**||
| [Webalkalmazás Linux rendszeren és MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Üzembe helyez egy Azure-webalkalmazást Linux rendszeren az Azure Database for MySQL-lel. |
| [Webalkalmazás Linux rendszeren és PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Üzembe helyez egy Azure-webalkalmazást Linux rendszeren az Azure Database for PostgreSQL-lel. |
|**Webalkalmazás csatlakoztatott erőforrásokkal**||
| [Webalkalmazás és MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Üzembe helyez egy Azure-webalkalmazást Windows rendszeren az Azure Database for MySQL-lel. |
| [Webalkalmazás és PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Üzembe helyez egy Azure-webalkalmazást Windows rendszeren az Azure Database for PostgreSQL-lel. |
| [Webalkalmazás SQL Database-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Üzembe helyez egy Azure-webalkalmazást és egy alapszintű szolgáltatási szintű SQL Database-adatbázist. |
| [Webalkalmazás Blob Storage-kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Üzembe helyez egy Azure-webalkalmazást egy Blob Storage-kapcsolati sztringgel. Ezután használhatja a Blob-tárolót a webalkalmazásból. |
| [Webalkalmazás létrehozása az Azure Cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Üzembe helyez egy Azure-webalkalmazást az Azure Cache a redis. |
|**App Service-környezet a PowerApps alkalmazáshoz**||
| [App Service Environment v2-környezet létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton. |
| [App Service Environment v2-környezet létrehozása ILB-címmel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton egy privát belső terheléselosztó címmel. |
| [Az alapértelmezett SSL-tanúsítvány konfigurálása egy ILB App Service-környezethez vagy egy ILB App Service Environment v2-környezethez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Egy ILB App Service-környezet vagy egy ILB App Service Environment v2-környezet használatához konfigurálja az alapértelmezett SSL-tanúsítványt. |
| | |
