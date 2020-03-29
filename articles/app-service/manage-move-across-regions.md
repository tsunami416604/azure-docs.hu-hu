---
title: Alkalmazás áthelyezése másik régióba
description: Ismerje meg, hogyan helyezheti át az App Service-erőforrásokat egyik régióból a másikba.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925708"
---
# <a name="move-an-app-service-app-to-another-region"></a>App Service-alkalmazás áthelyezése másik régióba

Ez a cikk ismerteti, hogyan helyezheti át az App Service-erőforrásokat egy másik Azure-régióba. Előfordulhat, hogy az erőforrásokat több okból is áthelyezi egy másik régióba. Például egy új Azure-régió előnyeinek kihasználásához, csak bizonyos régiókban elérhető funkciók vagy szolgáltatások üzembe helyezéséhez, a belső szabályzatés cégirányítási követelmények teljesítéséhez, vagy a kapacitástervezési követelményeknek való válaszul.

Az App Service-erőforrások régióspecifikusak, és nem helyezhetők át régiók között. Létre kell hoznia egy másolatot a meglévő App Service-erőforrások a célrégióban, helyezze át a tartalmat át az új alkalmazásba. Ha a forrásalkalmazás egyéni tartományt használ, ha végzett, [áttelepítheti azt a célrégióban lévő új alkalmazásba.](manage-custom-dns-migrate-domain.md)

Az alkalmazás másolásának megkönnyítése érdekében [klónozhat egy adott App Service-alkalmazást egy](app-service-web-app-cloning.md) másik régióban lévő App Service-csomagba, de [vannak korlátai,](app-service-web-app-cloning.md#current-restrictions)különösen, hogy nem támogatja a Linux-alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az App Service-alkalmazás az Azure-régióban, ahonnan át szeretne helyezni.
- Győződjön meg arról, hogy a célrégió támogatja az App Service és bármely kapcsolódó szolgáltatás, amelynek erőforrásait szeretné áthelyezni.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Előkészítés

Azonosítsa az összes, jelenleg használt App Service-erőforrást. Példa:

- App Service-alkalmazások
- [App Service-csomagok](overview-hosting-plans.md)
- [Telepítési bővítőhelyek](deploy-staging-slots.md)
- [Az Azure-ban vásárolt egyéni tartományok](manage-custom-dns-buy-domain.md)
- [SSL-tanúsítványok](configure-ssl-certificate.md)
- [Az Azure virtuális hálózat integrációja](web-sites-integrate-with-vnet.md)
- [Hibrid kapcsolatok](app-service-hybrid-connections.md).
- [Felügyelt identitások](overview-managed-identity.md)
- [Biztonsági mentés beállításai](manage-backup.md)

Bizonyos erőforrások, például az importált tanúsítványok vagy hibrid kapcsolatok más Azure-szolgáltatásokkal való integrációt tartalmaznak. Az erőforrások régiók közötti áthelyezéséről az adott szolgáltatások dokumentációjában olvashat.

## <a name="move"></a>Áthelyezés

1. [Készítsen biztonsági másolatot a forrásalkalmazásról.](manage-backup.md)
1. [Hozzon létre egy alkalmazást egy új App Service-csomagban, a célrégióban.](app-service-plan-manage.md#create-an-app-service-plan)
2. [A biztonsági másolatok visszaállítása a célalkalmazásban](web-sites-restore.md)
2. Ha egyéni tartományt használ, [elővételben kösse a célalkalmazáshoz,](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) `awverify.` és [engedélyezze a tartományt a célalkalmazásban.](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)
3. Állítson be minden mást a célalkalmazásban úgy, hogy megegyezik a forrásalkalmazással, és ellenőrizze a konfigurációt.
4. Ha készen áll arra, hogy az egyéni tartomány a célalkalmazásra mutasson, [rendelje hozzá újra a tartománynevet](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Forrásforrások karbantartása

Törölje a forrásalkalmazást és az App Service-csomagot. [A nem ingyenes csomagban lévő App Service-csomag díjat számít fel, még akkor is, ha egyetlen alkalmazás sem fut benne.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>További lépések

[Az Azure App Service alkalmazásklónozása a PowerShell használatával](app-service-web-app-cloning.md)