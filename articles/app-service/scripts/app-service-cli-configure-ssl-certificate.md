---
title: "Az Azure CLI parancsfájl minta - kötés egy egyéni az SSL-tanúsítványt egy |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - kötés egy egyéni SSL-tanúsítvány"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ab4e211bc2c3f7e99ab0246e7abba57795318245
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Egyéni SSL-tanúsítvány kötése a webes alkalmazás

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, majd az SSL-tanúsítvány egy egyéni tartománynév kötődik. Az ebben a példában a következőkre lesz szüksége:

* A tartományregisztráló DNS konfigurációs laphoz való hozzáférés.
* Egy érvényes. PFX-fájlt és a jelszót, az SSL-tanúsítvány feltöltése és kötni kívánt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az App Service-csomag létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [az alkalmazás-kulcs létrehozása](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [az alkalmazás kulcs config állomásnév hozzáadása](https://docs.microsoft.com/cli/azure/webapp/config/hostname#az_webapp_config_hostname_add) | Az egyéni tartománynév van leképezve a webes alkalmazás. |
| [az alkalmazás kulcs config ssl feltöltése](https://docs.microsoft.com/cli/azure/webapp/config/ssl#az_webapp_config_ssl_upload) | Az SSL-tanúsítvány feltölt egy webalkalmazást. |
| [az alkalmazás kulcs config ssl-kötés](https://docs.microsoft.com/cli/azure/webapp/config/ssl#az_webapp_config_ssl_bind) | A webes alkalmazás feltöltött SSL-tanúsítvány van kötve. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
