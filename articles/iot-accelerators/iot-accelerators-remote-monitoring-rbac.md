---
title: Távoli figyelés hozzáférés-vezérlése – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a szerepköralapú hozzáférés-vezérlés (RBAC) a távoli figyelési megoldás-gyorsító szolgáltatásban
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 817bc7624bb3a6b69d56265e40681287b7fc09fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969600"
---
# <a name="configure-role-based-access-control-in-the-remote-monitoring-solution-accelerator"></a>Szerepköralapú hozzáférés-vezérlés konfigurálása a távoli figyelési megoldás-gyorsító szolgáltatásban

Ez a cikk a szerepköralapú hozzáférés-vezérlés konfigurálását ismerteti a távoli figyelési megoldás-gyorsító szolgáltatásban. A szerepköralapú hozzáférés-vezérlés lehetővé teszi az egyes felhasználók hozzáférésének korlátozását a megoldás bizonyos funkcióihoz.

## <a name="default-settings"></a>Alapértelmezett beállítások

A távoli figyelési megoldás első telepítésekor két szerepkör létezik: **rendszergazda** és **csak olvasható**.

A **rendszergazdai** szerepkör bármely felhasználója teljes hozzáféréssel rendelkezik a megoldáshoz, beleértve az alábbi engedélyeket is. A **csak olvasási** szerepkörben lévő felhasználók csak a megoldás megtekintéséhez férhetnek hozzá.

| Engedély            | Rendszergazda | Csak olvasási engedély |
|----------------       |-------|-----------|
| Megoldás megtekintése         | Igen   | Igen       |
| Riasztások frissítése         | Igen   | Nem        |
| Riasztások törlése         | Igen   | Nem        |
| Eszközök létrehozása        | Igen   | Nem        |
| Eszközök frissítése        | Igen   | Nem        |
| Eszközök törlése        | Igen   | Nem        |
| Erőforráscsoportok létrehozása  | Igen   | Nem        |
| Eszközbeállítások frissítése  | Igen   | Nem        |
| Eszközbeállítások törlése  | Igen   | Nem        |
| Szabályok létrehozása          | Igen   | Nem        |
| Frissítési szabályok          | Igen   | Nem        |
| Szabályok törlése          | Igen   | Nem        |
| Feladatok létrehozása           | Igen   | Nem        |
| SIM-kezelés frissítése | Igen   | Nem        |

Alapértelmezés szerint a megoldást telepítő felhasználó automatikusan hozzárendeli a **rendszergazdai** szerepkört, és egy Azure Active Directory alkalmazás tulajdonosa. Az alkalmazás tulajdonosaként a Azure Portalon keresztül rendelhet hozzá szerepköröket más felhasználókhoz. Ha azt szeretné, hogy egy másik felhasználó szerepköröket rendeljen a megoldáshoz, akkor azt is be kell állítani a Azure Portal alkalmazás-tulajdonosként.

> [!NOTE]
> A megoldást telepítő felhasználó az **egyetlen személy** , aki közvetlenül a létrehozása után megtekintheti. Ha hozzáférést szeretne biztosítani másoknak az alkalmazás csak olvasható, rendszergazda vagy egyéni szerepkörként való megtekintéséhez, tekintse meg az alábbi utasításokat a felhasználók hozzáadása és eltávolítása című témakörben.

## <a name="add-or-remove-users"></a>Felhasználók hozzáadása vagy eltávolítása

Azure Active Directory alkalmazás tulajdonosaként a Azure Portal használatával hozzáadhat vagy eltávolíthat egy felhasználót a szerepkörhöz a távoli figyelési megoldásból. A következő lépések a távoli figyelési megoldás üzembe helyezésekor létrehozott [Azure Active Directory Enterprise alkalmazást](../active-directory/manage-apps/view-applications-portal.md) használják.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Győződjön meg arról, hogy a felhasználó az Ön [által használt címtárban van](../active-directory/fundamentals/add-users-azure-active-directory.md) . A [Microsoft Azure IoT megoldás-gyorsító](https://www.azureiotsolutions.com/Accelerators) webhelyre való bejelentkezéskor használt könyvtárat választotta. A könyvtár neve az [oldal](https://www.azureiotsolutions.com/Accelerators)jobb felső sarkában látható.

1. Keresse meg a **vállalati alkalmazást** a megoldásához a Azure Portal. A lista szűréséhez állítsa az **alkalmazás típusát** az **összes alkalmazásra**. Keresse meg az alkalmazást az alkalmazás neve alapján. Az alkalmazás neve a távoli figyelési megoldás neve. A következő képernyőképen a megoldás és az alkalmazás megjelenítendő nevei a **contoso-RM4**.

    ![Vállalati alkalmazás](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Győződjön meg arról, hogy az alkalmazás tulajdonosa, kattintson az alkalmazásra, majd a **tulajdonosok**elemre. A következő képernyőképen a **contoso rendszergazdája** a **contoso-RM4** alkalmazás tulajdonosa:

    ![A képernyőképen a tulajdonos kezelése lehetőség van kiválasztva, amely a contoso r m 4 alkalmazás tulajdonosait jeleníti meg.](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Ha nem tulajdonosa, meg kell kérnie egy meglévő tulajdonost, hogy felvegye Önt a listára. Csak a tulajdonosok rendelhetnek hozzá olyan alkalmazási szerepköröket, mint a **rendszergazda** , vagy **csak olvashatók** a többi felhasználó számára.

1. Az alkalmazás szerepköreihez rendelt felhasználók listájának megtekintéséhez kattintson a **felhasználók és csoportok**elemre.

1. Felhasználó hozzáadásához kattintson a **+ felhasználó hozzáadása**elemre, majd kattintson a **felhasználók és csoportok** elemre, és válasszon ki egy felhasználót a címtárból.

1. A felhasználó szerepkörhöz rendeléséhez kattintson a **szerepkör kiválasztása, nincs kiválasztva** lehetőségre, és válassza ki a **rendszergazda** vagy a **csak olvasás** szerepkört a felhasználó számára. Kattintson a **kiválasztás**, majd a **hozzárendelés**elemre.

    ![Válasszon szerepkört](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. A felhasználó mostantól hozzáférhet a távoli figyelési megoldáshoz a szerepkör által meghatározott engedélyekkel.

1. A felhasználókat törölheti az alkalmazásból a portál **felhasználók és csoportok** lapján.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

A távoli figyelési megoldás tartalmazza a **rendszergazda** és az **írásvédett** szerepköröket az első üzembe helyezéskor. Különböző engedélyekkel rendelkező egyéni szerepköröket adhat hozzá. Egyéni szerepkör definiálásához a következőket kell tennie:

- Vegyen fel egy új szerepkört az alkalmazásba a Azure Portal.
- Definiáljon egy házirendet az új szerepkörhöz a hitelesítési és engedélyezési szolgáltatásban.
- Frissítse a megoldás webes felhasználói felületét.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Egyéni szerepkör definiálása a Azure Portal

A következő lépések azt ismertetik, hogyan adhat hozzá szerepkört egy alkalmazáshoz Azure Active Directory-ben. Ebben a példában egy új szerepkört hoz létre, amely lehetővé teszi, hogy a tagok a távoli figyelési megoldásban hozzanak létre, frissítsenek és töröljön eszközöket.

1. Keresse meg a megoldáshoz tartozó **alkalmazás regisztrációját** a Azure Portal. Az alkalmazás neve a távoli figyelési megoldás neve. A következő képernyőképen a megoldás és az alkalmazás megjelenítendő nevei a **contoso-RM4**.

    ![Alkalmazásregisztráció](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Válassza ki az alkalmazást, majd kattintson a **manifest (jegyzékfájl**) elemre. Az alkalmazáshoz definiált két meglévő [alkalmazás-szerepkört](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) láthatja:

    ![Jegyzékfájl megtekintése](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Szerkessze a jegyzékfájlt egy **ManageDevices** nevű szerepkör hozzáadásához, ahogy az a következő kódrészletben látható. Szüksége lesz egy egyedi karakterláncra, például egy GUID azonosítóra az új szerepkör-azonosítóhoz. Létrehozhat egy új GUID azonosítót olyan szolgáltatás használatával, mint az [online GUID-generátor](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Mentse a módosításokat.

### <a name="define-a-policy-for-the-new-role"></a>Szabályzat definiálása az új szerepkörhöz

Miután hozzáadta a szerepkört az alkalmazáshoz a Azure Portalban, meg kell adnia egy házirendet a [roles.jsn](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) azon szerepkörhöz, amely az eszközök felügyeletéhez szükséges engedélyeket rendeli hozzá.

1. A [távoli monitorozási fürtszolgáltatások](https://github.com/Azure/remote-monitoring-services-dotnet) tárházának klónozása a githubról a helyi gépre.

1. Szerkessze a fájl **Auth/szolgáltatások/adat/házirendek/roles.jselemét** , és adja hozzá a **ManageDevices** szerepkörhöz tartozó házirendet az alábbi kódrészletben látható módon. Az **azonosító** és a **szerepkör** értékének meg kell egyeznie az alkalmazás jegyzékfájljának az előző szakaszban szereplő szerepkör-definícióval. Az engedélyezett műveletek listája lehetővé teszi, hogy valaki a **ManageDevices** -szerepkörben a megoldáshoz kapcsolódó eszközöket hozzon létre, frissítsen és töröljön:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Ha befejezte a **szolgáltatások/adatházirendek/roles.js** fájl szerkesztését, építse újra, majd telepítse újra a hitelesítési és engedélyezési szolgáltatást a megoldás-gyorsító felé.

### <a name="how-the-web-ui-enforces-permissions"></a>A webes felhasználói felület engedélyeinek érvényesítése

A webes felhasználói felület a [hitelesítés és engedélyezés szolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) használatával határozza meg, hogy a felhasználó milyen műveleteket végezhet el, és hogy milyen vezérlőelemek láthatók a felhasználói felületen. Ha például a megoldás neve **contoso-RM4**, a webes felhasználói felület lekéri az aktuális felhasználó számára engedélyezett műveletek listáját az alábbi kérelem elküldésével:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

A **ManageDevices** szerepkörben **Eszközkezelő** nevű felhasználó esetén a válasz a következő JSON-t tartalmazza a törzsben:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

A [webes felületen](https://github.com/Azure/pcs-remote-monitoring-webui/) lévő [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) következő kódrészlete azt mutatja be, hogy az engedélyek hogyan lesznek kikényszerítve a deklaratív módon:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

További információ: [védett összetevők](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). További engedélyeket is megadhat a [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) fájlban.

### <a name="how-the-microservices-enforce-permissions"></a>A szolgáltatásokra vonatkozó engedélyek érvényesítése

A webszolgáltatás a jogosulatlan API-kérések elleni védelemre vonatkozó engedélyeket is megtekintheti. Ha egy webszolgáltatás egy API-kérést kap, a dekódolja és érvényesíti a JWT jogkivonatot a felhasználó szerepköréhez tartozó felhasználói azonosító és engedélyek beszerzéséhez.

A [IoTHub Manager-kezelőben](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)a [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) fájl következő kódrészlete az engedélyek kikényszerített módját mutatja be:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan valósítja meg a szerepköralapú hozzáférés-vezérlést a távoli figyelési megoldás gyorsítása során.

Lásd: a [Time Series Insights Explorer hozzáférés-vezérlésének konfigurálása](iot-accelerators-remote-monitoring-rbac-tsi.md) a távoli figyelési megoldás-gyorsító Time Series Insights Explorer elérésének kezelésével kapcsolatos információkért.

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd: [a szolgáltatás testreszabása és újbóli üzembe helyezése](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->