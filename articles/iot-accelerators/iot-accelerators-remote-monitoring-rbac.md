---
title: Távoli figyelés hozzáférés-vezérlés – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálhatja a távoli figyelési megoldásgyorsító szerepköralapú hozzáférés-vezérlést (RBAC)
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: fccdc4ac40878060b94d495b8895e2a128c9477c
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716111"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító a szerepköralapú hozzáférés-vezérlés konfigurálása

Ez a cikk ismerteti a szerepköralapú hozzáférés-vezérlés konfigurálása a távoli figyelési megoldásgyorsító. Szerepköralapú hozzáférés-vezérlés lehetővé teszik a megoldásban a funkciók korlátozza a hozzáférést az egyéni felhasználók számára.

## <a name="default-settings"></a>Alapértelmezett beállítások

Ha először telepíti a távoli figyelési megoldás, vannak-e két szerepkör: **rendszergazdai** és **csak olvasható**.

A felhasználói a **rendszergazdai** szerepkör a megoldás teljes körű hozzáféréssel rendelkezik. A felhasználó a **csak olvasható** szerepkör nem hajtható végre az alábbi műveletek bármelyikét:

- Riasztás frissítése
- Riasztás törlése
- Eszközök létrehozása
- Eszközök frissítése
- Eszközök törlése
- Eszközcsoportok létrehozása
- Az eszközcsoportok frissítése
- Az eszközcsoportok törlése
- Szabályok létrehozása
- Frissítési szabályok
- Szabályok törlése
- Feladatok létrehozása
- A frissítéskezelés SIM

A távoli figyelési megoldás központi telepítését végző személy automatikusan hozzá lesz rendelve a **rendszergazdai** szerepkör és a egy Azure Active Directory-alkalmazás tulajdonosa. Alkalmazás tulajdonosként, szerepköröket rendelhetnek más felhasználók az Azure Portalon.

Ha azt szeretné, ha a megoldás a szerepköröket egy másik felhasználó, akkor is meg kell application tulajdonosként az Azure Portalon.

## <a name="add-or-remove-users"></a>Felhasználók hozzáadása vagy eltávolítása

Az Azure portal használatával adja hozzá, vagy távolítsa el a felhasználók a távoli figyelési megoldás. Az alábbi lépések az a [Azure Active Directory vállalati alkalmazás](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) , amely a távoli figyelési megoldás üzembe helyezésekor létrejött az Ön számára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Ellenőrizze a [felhasználó szerepel a könyvtár](../active-directory/fundamentals/add-users-azure-active-directory.md) használ. Döntött, hogy bejelentkezett a könyvtárat a [a Microsoft Azure IoT-Megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators) hely. A könyvtár neve jelenik meg a jobb felső sarkában a [oldal](https://www.azureiotsolutions.com/Accelerators).

1. Keresse meg a **vállalati alkalmazás** a megoldás az Azure Portalon. Egyszer, a lista szűréséhez beállításával **alkalmazástípus** való **minden alkalmazás**. Keresse meg az alkalmazás által alkalmazás nevére. Az alkalmazás nevét a távoli figyelési megoldás neve. Az alábbi képernyőképen a megoldást és az alkalmazás megjelenített nevek a következők **contoso-rm4**.

    ![Vállalati alkalmazás](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Ellenőrizze, hogy az alkalmazás ezután parancsra kattintva Ön az alkalmazás tulajdonosának **tulajdonosok**. Az alábbi képernyőképen **Contoso rendszergazdai** tulajdonosa a **contoso-rm4** alkalmazás:

    ![Tulajdonosok](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Ha nem egy olyan tulajdonost, kérje meg egy meglévő tulajdonosát hozzáadása a listához szeretné. Csak a tulajdonosok például alkalmazás-szerepkörök is hozzárendelhet **rendszergazdai** vagy **csak olvasható** más felhasználók számára.

1. Az alkalmazás-szerepkörökhöz rendelt felhasználók listájának megtekintéséhez kattintson **felhasználók és csoportok**.

1. A felhasználó hozzáadásához kattintson **+ Hozzáadás felhasználó**, és kattintson a **felhasználókat és csoportokat, nincs kiválasztott** , válasszon ki egy felhasználót a címtárból.

1. A felhasználói szerepkörhöz rendeléséhez kattintson **, nincs kiválasztott szerepkör kiválasztása** , és válassza a **rendszergazdai** vagy **csak olvasható** szerepkört a felhasználóhoz. Kattintson a **kiválasztása**, és kattintson a **hozzárendelése**.

    ![Szerepkör kiválasztása](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. A felhasználó most már elérhető a távoli figyelési megoldás a szerepkör által meghatározott engedélyekkel.

1. Az alkalmazás a felhasználó törölheti az a **felhasználók és csoportok** lapot a portálon.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

A távoli figyelési megoldás magában foglalja a **rendszergazdai** és **csak olvasható** szerepkörök első telepítésekor. Egyéni szerepkörök az engedélyek eltérő halmazát adhat hozzá. Egyéni szerepkör definiálásához kell tennie:

- Új szerepkör hozzáadása az alkalmazáshoz az Azure Portalon.
- Adja meg az új szerepkör-házirend a hitelesítési és engedélyezési mikroszolgáltatás.
- A megoldás webes felhasználói felület frissítéséhez.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Adjon meg egy egyéni biztonsági szerepkört a az Azure Portalon

A következő lépések bemutatják, hogyan szerepkör hozzáadása az Azure Active Directory-alkalmazás. Ebben a példában hozzon létre egy új szerepkör, amely lehetővé teszi a tagok létrehozása, frissítése és törlése a távoli figyelési megoldásban az eszközök számára.

1. Keresse meg a **alkalmazásregisztráció** a megoldás az Azure Portalon. Az alkalmazás nevét a távoli figyelési megoldás neve. Az alábbi képernyőképen a megoldást és az alkalmazás megjelenített nevek a következők **contoso-rm4**.

    ![Appok regisztrálása](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Válassza ki az alkalmazását, és kattintson a **Manifest**. Láthatja, hogy a két meglévő [alkalmazás-szerepkörök](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) az alkalmazáshoz meghatározott:

    ![Jegyzék megtekintése](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Egy nevű szerepkör hozzáadása a jegyzékfájl szerkesztése **ManageDevices** az alábbi kódrészletben látható módon. Szüksége lesz egy egyedi karakterlánccá, például egy GUID Azonosítót az új szerepkör-azonosítót. Létrehozhat egy új GUID-szolgáltatást használ, mint például a [Online GUID-generátor](https://www.guidgenerator.com/):

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

### <a name="define-a-policy-for-the-new-role"></a>Meghatározhat egy olyan szabályzatot, az új szerepkör

Miután a szerepkör hozzáadása az alkalmazáshoz az Azure Portalon, meg kell határoznia egy szabályzatot a [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) a szerepkör, amely hozzárendeli az eszközök kezeléséhez szükséges engedélyekkel.

1. Klónozás a [távoli figyelés Mikroszolgáltatások](https://github.com/Azure/remote-monitoring-services-dotnet) GitHub-adattár helyi számítógépre.

1. Szerkessze a **auth/Services/data/policies/roles.json** fájlját, hogy a szabályzat a **ManageDevices** szerepkör az alábbi kódrészletben látható módon. A **azonosító** és **szerepkör** értékeknek egyezniük kell a szerepkör-definíció az előző szakaszban az alkalmazásjegyzékben. Engedélyezett műveletek listája lehetővé teszi, hogy valaki van a **ManageDevices** szerepkör létrehozása, frissítése és törlése a megoldáshoz csatlakoztatott eszközökre:

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

1. Miután végzett, szerkesztését a **Services/data/policies/roles.json** fájlt, újraépítése és ismételt üzembe helyezése a hitelesítési és engedélyezési mikroszolgáltatást a megoldásgyorsító.

### <a name="how-the-web-ui-enforces-permissions"></a>Hogyan érvénybe lépteti a webes felhasználói felületen az engedélyek

A webes felhasználói Felületet használja a [hitelesítési és engedélyezési mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) együttműködve meghatározza milyen műveleteket egy felhasználó számára engedélyezett időt vesz igénybe, és milyen vezérlők láthatók-e a felhasználói felületen. Például, ha a megoldás neve **contoso-rm4**, a webes felhasználói felületen kéri le az aktuális felhasználó számára engedélyezett műveletek listáját az alábbi kérelem küldésével:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

A felhasználó nevű **Eszközkezelő** a a **ManageDevices** szerepkör, a válasz törzsében is tartalmaz a következő JSON:

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

Az alábbi kódrészletet a [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) a a [webes felhasználói Felületét](https://github.com/Azure/pcs-remote-monitoring-webui/) bemutatja, hogyan az engedélyek kényszerített deklaratív:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

További információkért lásd: [védett összetevők](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Megadhatja, hogy a további engedélyeket a [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) fájlt.

### <a name="how-the-microservices-enforce-permissions"></a>Hogyan a mikroszolgáltatások kényszerítése a engedélyek

A mikroszolgáltatások engedélyek jogosulatlan API-kérelmek ellen is ellenőrizheti. Amikor egy mikroszolgáltatás-API-kérést kap, dekódol, és érvényesíti a JWT jogkivonatot beolvasni a felhasználói Azonosítót és a felhasználói szerephez tartozó engedélyeket.

A következő kódrészlet a a [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) fájlt a [IoTHub Manager mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager), bemutatja, hogyan tartatja be az engedélyeket:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan szerepköralapú hozzáférés-vezérlők a távoli figyelési megoldásgyorsító találhatók meg.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd: [testreszabása és ismételt mikroszolgáltatások üzembe helyezése](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->