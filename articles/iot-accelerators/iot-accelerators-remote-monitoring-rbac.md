---
title: Távfigyelés hozzáférés-vezérlése - Azure | Microsoft dokumentumok
description: Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálható a szerepköralapú hozzáférés-vezérlés (RBAC) a Távoli figyelési megoldás gyorsítójában
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: b0c9699bccbb539c9617fac2f3296483139e7188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67203157"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Szerepköralapú hozzáférés-vezérlés konfigurálása a Távfigyelés i.

Ez a cikk a szerepköralapú hozzáférés-vezérlők konfigurálásáról nyújt tájékoztatást a távfigyelési megoldás gyorsítójában. A szerepköralapú hozzáférés-vezérléssel korlátozhatja az egyes felhasználók hozzáférését a megoldás bizonyos szolgáltatásaira.

## <a name="default-settings"></a>Alapértelmezett beállítások

A távfigyelési megoldás első telepítésekor két szerepkör van: **rendszergazda** és **írásvédett.**

A **Rendszergazda** szerepkörbármely felhasználója teljes hozzáféréssel rendelkezik a megoldáshoz, beleértve az alábbi engedélyeket is. Az **Írásvédett szerepkörben** szereplő felhasználók csak a megoldás megtekintéséhez férhetnek hozzá.

| Engedély            | Rendszergazda | Csak olvasási engedély |
|----------------       |-------|-----------|
| Megoldás megtekintése         | Igen   | Igen       |
| Riasztások frissítése         | Igen   | Nem        |
| Riasztások törlése         | Igen   | Nem        |
| Eszközök létrehozása        | Igen   | Nem        |
| Eszközök frissítése        | Igen   | Nem        |
| Eszközök törlése        | Igen   | Nem        |
| Eszközcsoportok létrehozása  | Igen   | Nem        |
| Eszközcsoportok frissítése  | Igen   | Nem        |
| Eszközcsoportok törlése  | Igen   | Nem        |
| Szabályok létrehozása          | Igen   | Nem        |
| Szabályok frissítése          | Igen   | Nem        |
| Szabályok törlése          | Igen   | Nem        |
| Feladatok létrehozása           | Igen   | Nem        |
| A SIM-kezelés frissítése | Igen   | Nem        |

Alapértelmezés szerint a felhasználó, aki a megoldást telepítette, automatikusan hozzávan rendelve a **rendszergazdai** szerepkörhöz, és az Azure Active Directory-alkalmazás tulajdonosa. Alkalmazástulajdonosként szerepköröket rendelhet más felhasználókhoz az Azure Portalon keresztül. Ha azt szeretné, hogy egy másik felhasználó szerepköröket rendeljen hozzá a megoldáshoz, azokkal az Azure Portalon is be kell állítani őket alkalmazástulajdonosként.

> [!NOTE]
> A felhasználó, aki telepítette a megoldást az **egyetlen személy,** aki megtekintheti azt közvetlenül a létrehozása után. Ha hozzáférést szeretne adni másoknak az alkalmazás írásvédettként, rendszergazdaként vagy egyéni szerepkörként való megtekintéséhez, olvassa el az alábbi utasításokat a felhasználók hozzáadásáról vagy eltávolításáról.

## <a name="add-or-remove-users"></a>Felhasználók hozzáadása és eltávolítása

Az Azure Active Directory-alkalmazás tulajdonosaként az Azure Portalon hozzáadhat vagy eltávolíthat egy felhasználót egy szerepkörhöz a távoli figyelési megoldásból. A következő lépések az [Azure Active Directory vállalati alkalmazást](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) használják, amely a távoli figyelési megoldás telepítésekor jött létre.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Ellenőrizze, hogy a felhasználó a használt [könyvtárban](../active-directory/fundamentals/add-users-azure-active-directory.md) van-e. A [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators) webhelyére való bejelentkezéskor használt könyvtárat választotta. A könyvtárnév a [lap](https://www.azureiotsolutions.com/Accelerators)jobb felső sarkában látható.

1. Keresse meg a **megoldás enterprise-alkalmazását** az Azure Portalon. Miután ott, szűrje a listát beállításával **Alkalmazás típusa** **minden alkalmazás**. Keresse meg az alkalmazást az alkalmazás neve alapján. Az alkalmazás neve a távoli figyelési megoldás neve. A következő képernyőképen a megoldás és az alkalmazás megjelenítési **nevei contoso-rm4**.

    ![Vállalati alkalmazás](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Az alkalmazás tulajdonosának ellenőrzéséhez kattintson az alkalmazásra, majd a **Tulajdonosok**parancsra. A következő képernyőképen a **Contoso-rendszergazda** a **contoso-rm4** alkalmazás tulajdonosa:

    ![Tulajdonosok](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Ha ön nem tulajdonos, meg kell kérnie egy meglévő tulajdonost, hogy vegye fel önt a listára. Csak a tulajdonosok rendelhetnek hozzá alkalmazásszerepköröket, például **a rendszergazda** vagy **az írásvédett** más felhasználókhoz.

1. Az alkalmazásban szerepkörökhöz rendelt felhasználók listájának megtekintéséhez kattintson a **Felhasználók és csoportok**elemre.

1. Felhasználó hozzáadásához kattintson **a + Felhasználó hozzáadása**gombra , majd a Felhasználók és csoportok **gombra,** majd a Nincs lehetőség parancsra a felhasználó nak a könyvtárból való kiválasztásához.

1. Ha a felhasználót szerepkörhöz szeretné rendelni, kattintson a **Szerepkör kiválasztása, Nincs kijelölt elemre,** és válassza a **felhasználó Rendszergazda** vagy **Csak olvasható** szerepkörét. Kattintson **a Kijelölés**gombra, majd a **Hozzárendelés gombra.**

    ![Szerepkör kiválasztása](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. A felhasználó most már hozzáférhet a távoli figyelési megoldáshoz a szerepkör által meghatározott engedélyekkel.

1. A felhasználók at törölheti az alkalmazásból a **portál Felhasználók és csoportok** lapján.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

A távoli figyelési megoldás tartalmazza a **rendszergazdai** és **csak olvasható** szerepköröket, amikor először telepíti. Különböző engedélykészletekkel rendelkező egyéni szerepköröket adhat hozzá. Egyéni szerepkör definiálásához a következőket kell megadnia:

- Adjon hozzá egy új szerepkört az alkalmazáshoz az Azure Portalon.
- A hitelesítési és engedélyezési mikroszolgáltatás új szerepkörének házirendjének definiálása.
- Frissítse a megoldás webes felhasználói felületét.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Egyéni szerepkör definiálása az Azure Portalon

Az alábbi lépések ismertetik, hogyan adhat hozzá egy szerepkört egy alkalmazáshoz az Azure Active Directoryban. Ebben a példában egy új szerepkört hoz létre, amely lehetővé teszi a tagok számára, hogy eszközöket hozzanak létre, frissítsenek és töröljenek a Távoli figyelési megoldásban.

1. Keresse meg a megoldás **alkalmazásregisztrációját** az Azure Portalon. Az alkalmazás neve a távoli figyelési megoldás neve. A következő képernyőképen a megoldás és az alkalmazás megjelenítési **nevei contoso-rm4**.

    ![Alkalmazásregisztráció](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Jelölje ki az alkalmazást, majd kattintson **a Jegyzékfájl gombra.** Az alkalmazáshoz definiált két meglévő [alkalmazásszerepkör](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) látható:

    ![Jegyzékfájl megtekintése](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. A jegyzékfájl szerkesztésével adjon hozzá egy **ManageDevices** nevű szerepkört, ahogy az a következő kódrészletben látható. Szüksége van egy egyedi karakterláncra, például egy GUID azonosítóra az új szerepkörazonosítóhoz. Új GUID-t hozhat létre egy olyan szolgáltatás használatával, mint például az [online GUID generátor:](https://www.guidgenerator.com/)

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

### <a name="define-a-policy-for-the-new-role"></a>Házirend definiálása az új szerepkörhöz

Miután hozzá adja a szerepkört az alkalmazáshoz az Azure Portalon, meg kell határoznia egy [szabályzatot roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) a szerepkörhöz, amely hozzárendeli az eszközök kezeléséhez szükséges engedélyeket.

1. Klónozza a [távoli figyelési mikroszolgáltatások](https://github.com/Azure/remote-monitoring-services-dotnet) tárháza a GitHubról a helyi számítógépre.

1. Az **auth/Services/data/policies/roles.json** fájl szerkesztésével adja hozzá a **ManageDevices** szerepkör házirendjének házirendjeit a következő kódrészletben látható módon. Az **azonosító** és **a szerepkör** értékek egyeznie kell a szerepkör-definíció az alkalmazásjegyzékben az előző szakaszból. Az engedélyezett műveletek listája lehetővé teszi, hogy a **ManageDevices** szerepkörben valaki a megoldáshoz csatlakoztatott eszközöket hozzon létre, frissítsen és töröljön:

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

1. Miután befejezte a **Szolgáltatások/adatok/szabályzatok/roles.json** fájl szerkesztését, építse újra és telepítse újra a hitelesítési és engedélyezési mikroszolgáltatást a megoldásgyorsítóhoz.

### <a name="how-the-web-ui-enforces-permissions"></a>Hogyan érvényesíti a webes felhasználói felület az engedélyeket?

A webes felhasználói felület a [hitelesítési és engedélyezési mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) segítségével határozza meg, hogy a felhasználó milyen műveleteket hajthet el, és milyen vezérlők láthatók a felhasználói felületen. Ha például a megoldást **contoso-rm4-nek**nevezik, a webes felhasználói felület a következő kérés elküldésével lekéri az aktuális felhasználó számára engedélyezett műveletek listáját:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

A **ManageDevices** szerepkörben az **Eszközkezelő** nevű felhasználó esetében a válasz a következő JSON-t tartalmazza a szervezetben:

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

A következő kódrészlet az [eszköztörlés.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) eszközről a [webes felhasználói felületen](https://github.com/Azure/pcs-remote-monitoring-webui/) bemutatja, hogyan kényszerítik ki deklaratív módon az engedélyeket:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

További információt a Védett összetevők című [témakörben talál.](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md) Az [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) fájlban további engedélyeket is megadhat.

### <a name="how-the-microservices-enforce-permissions"></a>Hogyan kényszerítik ki a mikroszolgáltatások az engedélyeket?

A mikroszolgáltatások is ellenőrzik az engedélyeket a jogosulatlan API-kérelmek elleni védelem érdekében. Amikor egy mikroszolgáltatás API-kérelmet kap, dekódolja és érvényesíti a JWT-jogkivonatot a felhasználói azonosító és a felhasználó szerepköréhez társított engedélyek lekéréséhez.

Az [ioTHub-kezelő mikroszolgáltatásának](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) fájljából a következő kódrészlet bemutatja az engedélyek érvényesítésének módját:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan szerepköralapú hozzáférés-vezérlések vannak megvalósítva a távoli figyelési megoldás gyorsítójában.

A [Time Series Insights-kezelő hozzáférés-vezérléskonfigurálása](iot-accelerators-remote-monitoring-rbac-tsi.md) című témakörben talál információt a Time Series Insights-kezelő höz való hozzáférés kezeléséről a Távfigyelési megoldás gyorsítójában.

A távfigyelési megoldásgyorsítóval kapcsolatos további információkért lásd: [Távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távfigyelési megoldás testreszabásáról a [Mikroszolgáltatás testreszabása és újratelepítése](iot-accelerators-microservices-example.md) című témakörben talál további információt.
<!-- Next tutorials in the sequence -->