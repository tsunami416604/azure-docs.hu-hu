---
title: Azure Resource Manager-sablonminták – App Service | Microsoft Docs
description: Az Azure Resource Manager-sablonminták az App Service-ben
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 842ec98245522095334b9f17e8c12292b7c1dda8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035440"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service-ben az Azure Resource Manager-sablonokkal

A következő táblázat Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz az Azure App Service-ben. Javaslatok a sablonok létrehozásakor előforduló gyakori hibák, lásd: [útmutatójában alkalmazások az Azure Resource Manager-sablonok](deploy-resource-manager-template.md).

A JSON-szintaxist és a tulajdonságokat App Services-erőforrások kapcsolatos további információkért lásd: [Microsoft.Web erőforrástípusok](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Alkalmazás üzembe helyezése**||
| [App Service-csomag és az alapszintű Linux-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Üzembe helyez egy App Service-alkalmazás, amely Linux van konfigurálva. |
| [App Service-csomag és az alapszintű Windows-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Üzembe helyez egy App Service-alkalmazás, amely Windows van konfigurálva. |
| [Egy GitHub-adattárhoz kapcsolódó alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Üzembe helyez egy App Service-alkalmazást, amely a githubról kér le kódokat. |
| [Egyéni üzembehelyezési pontokat használó alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Az egyéni üzembehelyezési pontok/környezetek egy App Service alkalmazást helyezünk üzembe. |
|**Alkalmazás konfigurálása**||
| [Tanúsítvány a Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Üzembe helyez egy App Service-ben tanúsítvány a az Azure Key Vault titkos kulcsából, és SSL-kötés létrehozásához használja. |
| [Egyéni tartományt használó alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Üzembe helyez egy App Service-alkalmazás, amely egyéni gazdagépnévvel rendelkezik. |
| [Alkalmazás és a egy egyéni tartományt és SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Üzembe helyez egy App Service-alkalmazás, amely egyéni gazdagépnévvel rendelkezik, és lekér egy tanúsítványt a Key Vault SSL-kötés létrehozásához. |
| [Alkalmazás GoLang bővítménnyel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Üzembe helyez egy App Service alkalmazást a Golang webhelybővítménnyel. Ezután futtathatja a Golangben fejlesztett webalkalmazásokat az Azure-ban. |
| [Java 8- és Tomcat 8-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Üzembe helyez egy App Service-alkalmazást Java 8-és Tomcat 8-támogatással. Ezután Java-alkalmazásokat futtathat az Azure-ban. |
|**Linux-alkalmazás csatlakoztatott erőforrásokkal**||
| [Alkalmazás Linux rendszeren és MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Üzembe helyez egy App Service-alkalmazás Linux rendszeren az Azure Database for MySQL-hez. |
| [Alkalmazás Linux rendszeren és PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Üzembe helyez egy App Service-alkalmazás Linux rendszeren az Azure Database for postgresql-hez. |
|**Alkalmazás csatlakoztatott erőforrásokkal**||
| [Alkalmazás és MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Üzembe helyez App Service-alkalmazások, a Windows az Azure Database for MySQL-hez. |
| [Alkalmazás-és PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Üzembe helyez App Service-alkalmazások, a Windows az Azure Database for postgresql-hez. |
| [Alkalmazás és a egy SQL-adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Üzembe helyez egy App Service-alkalmazás és a egy alapszintű szolgáltatási szintű SQL database. |
| [Az alkalmazás egy Blob storage-kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Üzembe helyez egy App Service-alkalmazás egy Azure Blob tárolási kapcsolati karakterlánccal. Ezután használhatja a Blob storage-az alkalmazást. |
| [Alkalmazás és az Azure Cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Üzembe helyez egy App Service-alkalmazás az Azure Cache redis. |
|**App Service-környezet a PowerApps alkalmazáshoz**||
| [App Service Environment v2-környezet létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton. |
| [App Service Environment v2-környezet létrehozása ILB-címmel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton egy privát belső terheléselosztó címmel. |
| [Az alapértelmezett SSL-tanúsítvány konfigurálása egy ILB App Service-környezethez vagy egy ILB App Service Environment v2-környezethez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Egy ILB App Service-környezet vagy egy ILB App Service Environment v2-környezet használatához konfigurálja az alapértelmezett SSL-tanúsítványt. |
| | |
