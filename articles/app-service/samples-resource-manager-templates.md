---
title: Azure Resource Manager-sablonminták – App Service | Microsoft Docs
description: Azure Resource Manager sablon minták a App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 83bb357544d9069da1c86f583eaca5a470953ce8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066473"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service Azure Resource Manager sablonjai

Az alábbi táblázat a Azure App Service Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaz. Az alkalmazás-sablonok létrehozásakor előforduló gyakori hibák elkerülésével kapcsolatos javaslatokért tekintse [meg az alkalmazások Azure Resource Manager-sablonokkal való üzembe helyezésének útmutatását](deploy-resource-manager-template.md).

A App Services erőforrások JSON-szintaxisával és tulajdonságaival kapcsolatos további információkért lásd: [Microsoft. Web Resource types](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Alkalmazás üzembe helyezése**||
| [App Service csomag és alapszintű Linux-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Üzembe helyez egy Linux rendszerhez konfigurált App Service alkalmazást. |
| [App Service csomag és alapszintű Windows-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Központilag telepíti a Windows rendszerhez konfigurált App Service alkalmazást. |
| [GitHub-adattárhoz csatolt alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Üzembe helyez egy App Service alkalmazást, amely kódot húz le a GitHubról. |
| [Alkalmazás egyéni üzembe helyezési pontokkal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Üzembe helyez egy App Service alkalmazást egyéni üzembe helyezési pontokkal/környezetekkel. |
|**Alkalmazás konfigurálása**||
| [Alkalmazás-tanúsítvány Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Üzembe helyez egy App Service alkalmazás-tanúsítványt egy Azure Key Vault titkos kulcsból, és az SSL-kötéshez használja azt. |
| [Alkalmazás egyéni tartománnyal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Üzembe helyez egy App Service alkalmazást egyéni állomásnévvel. |
| [Alkalmazás egyéni tartománnyal és SSL-lel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Üzembe helyez egy App Service alkalmazást egyéni állomásnévvel, és beolvassa az alkalmazás tanúsítványát az Key Vaultból az SSL-kötéshez. |
| [Alkalmazás GoLang bővítménnyel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Üzembe helyez egy App Service alkalmazást a Golang hely bővítménnyel. Ezután futtathatja a Golangben fejlesztett webalkalmazásokat az Azure-ban. |
| [Alkalmazás Java 8 és tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Üzembe helyez egy App Service alkalmazást a Java 8 és a Tomcat 8 használata esetén. Ezután Java-alkalmazásokat futtathat az Azure-ban. |
|**Linux-alkalmazás csatlakoztatott erőforrásokkal**||
| [Alkalmazás Linuxon MySQL-vel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Központilag telepít egy App Service alkalmazást Linux rendszeren, Azure Database for MySQL. |
| [Alkalmazás Linuxon a PostgreSQL használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Központilag telepít egy App Service alkalmazást Linux rendszeren, Azure Database for PostgreSQL. |
|**Alkalmazás csatlakoztatott erőforrásokkal**||
| [Alkalmazás MySQL-sel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Üzembe helyez egy App Service alkalmazást a Windows rendszeren Azure Database for MySQL. |
| [Alkalmazás PostgreSQL-sel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Üzembe helyez egy App Service alkalmazást a Windows rendszeren Azure Database for PostgreSQL. |
| [Alkalmazás SQL-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Üzembe helyez egy App Service alkalmazást és egy SQL-adatbázist az alapszintű szolgáltatási szinten. |
| [Alkalmazás blob Storage-kapcsolatban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Üzembe helyez egy App Service alkalmazást egy Azure Blob Storage-beli kapcsolatok karakterláncával. Ezután használhatja a blob Storage-t az alkalmazásból. |
| [Alkalmazás egy Azure cache for Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Üzembe helyez egy App Service alkalmazást a Redis készült Azure cache-sel. |
|**App Service-környezet a PowerApps alkalmazáshoz**||
| [App Service Environment v2-környezet létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton. |
| [App Service Environment v2-környezet létrehozása ILB-címmel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton egy privát belső terheléselosztó címmel. |
| [Az alapértelmezett SSL-tanúsítvány konfigurálása egy ILB App Service-környezethez vagy egy ILB App Service Environment v2-környezethez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Egy ILB App Service-környezet vagy egy ILB App Service Environment v2-környezet használatához konfigurálja az alapértelmezett SSL-tanúsítványt. |
| | |
