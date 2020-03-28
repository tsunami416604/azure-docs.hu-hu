---
title: Az Azure Resource Manager sablonmintái
description: Keresse meg az Azure Resource Manager sablonmintákat néhány gyakori App Service-forgatókönyvekhez. Ismerje meg, hogyan automatizálhatja az App Service központi telepítési vagy felügyeleti feladatait.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: b1d5f20ccd2f2c637d7db668af10ef331947d018
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74971196"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Azure Resource Manager-sablonok az App Service szolgáltatáshoz

Az alábbi táblázat az Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz az Azure App Service-hez. Az alkalmazássablonok létrehozásakor előforduló gyakori hibák elkerülésére vonatkozó javaslatokért [olvassa el az Alkalmazások Azure Resource Manager-sablonokkal történő telepítésének útmutatója témakört.](deploy-resource-manager-template.md)

A JSON szintaxisáról és az App Services-erőforrások tulajdonságairól a [Microsoft.Web erőforrástípusok](/azure/templates/microsoft.web/allversions)című témakörben olvashat.

| | |
|-|-|
|**Alkalmazás telepítése**||
| [App Service-csomag és alapszintű Linux-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Linuxra konfigurált App Service-alkalmazás központi telepítése. |
| [App Service-csomag és alapszintű Windows-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | A Windows rendszerhez konfigurált App Service-alkalmazás központi telepítése. |
| [GitHub-tárházhoz csatolt alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Telepítegy App Service-alkalmazást, amely lekéri a kódot a GitHubról. |
| [Alkalmazás egyéni központi telepítési helyekkel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Telepíti az App Service-alkalmazást egyéni központi telepítési helyekkel/környezetekkel. |
|**Alkalmazás konfigurálása**||
| [Alkalmazástanúsítvány a Key Vaultból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Telepíti az App Service-alkalmazás tanúsítványát egy Azure Key Vault titkos kulcsból, és ssl-kötéshez használja. |
| [Alkalmazás egyéni tartománnyal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Telepíti az Egyéni állomásnévvel rendelkező App Service-alkalmazást. |
| [Alkalmazás egyéni tartománnyal és SSL-lel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Telepíti az App Service-alkalmazást egyéni állomásnévvel, és lekéri az alkalmazástanúsítványt a Key Vault SSL-kötéshez. |
| [GoLang-bővítményes alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Telepítegy App Service-alkalmazást a Golang-webhelybővítménylel. Ezután futtathatja a Golangben fejlesztett webalkalmazásokat az Azure-ban. |
| [Alkalmazás Java 8-as és Tomcat 8-as verzióval](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Telepítegy App Service-alkalmazást, amelyen engedélyezve van a Java 8 és a Tomcat 8. Ezután Java-alkalmazásokat futtathat az Azure-ban. |
|**Alkalmazás védelme**||
| [Az Azure Application Gateway alkalmazásba integrált alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Telepíti az App Service-alkalmazást és egy Application Gateway-t, és elkülöníti a forgalmat a szolgáltatás végpontés a hozzáférési korlátozások használatával. |
|**Linux alkalmazás csatlakoztatott erőforrásokkal**||
| [Alkalmazás Linuxon a MySQL-rel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Egy App Service-alkalmazást telepít Linuxon az Azure Database for MySQL használatával. |
| [Alkalmazás Linuxon a PostgreSQL-rel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Egy App Service-alkalmazást telepít Linuxon az Azure Database for PostgreSQL segítségével. |
|**Alkalmazás csatlakoztatott erőforrásokkal**||
| [Alkalmazás a MySQL-rel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Egy App Service-alkalmazást telepít a Windows-on az Azure Database for MySQL használatával. |
| [Alkalmazás a PostgreSQL-rel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Egy App Service-alkalmazást telepít a Windows-on az Azure Database for PostgreSQL segítségével. |
| [Alkalmazás SQL-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Telepítegy App Service-alkalmazást és egy SQL-adatbázist az alapszintű szolgáltatásszinten. |
| [Alkalmazás Blob-tárolókapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Egy App Service-alkalmazás üzembe helyezése egy Azure Blob storage-kapcsolati karakterlánccal. Ezután használhatja a Blob storage az alkalmazásból. |
| [Alkalmazás A Redis Azure-gyorsítótárral](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Egy App Service-alkalmazás központi telepítése egy Azure-gyorsítótár redis. |
|**App Service-környezet**||
| [App Service Environment v2-környezet létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton. |
| [App Service Environment v2-környezet létrehozása ILB-címmel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton egy privát belső terheléselosztó címmel. |
| [Az alapértelmezett SSL-tanúsítvány konfigurálása egy ILB App Service-környezethez vagy egy ILB App Service Environment v2-környezethez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Egy ILB App Service-környezet vagy egy ILB App Service Environment v2-környezet használatához konfigurálja az alapértelmezett SSL-tanúsítványt. |
| | |
