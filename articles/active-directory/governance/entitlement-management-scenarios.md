---
title: Gyakori forgatókönyvek az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Ismerje meg a Azure Active Directory jogosultságok kezelése (előzetes verzió) gyakori forgatókönyveit.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8e7709abcc97baac9bf657b9fff2110cb2e72c1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489035"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Gyakori forgatókönyvek az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Több módon is konfigurálhatja a jogosultságok kezelését a szervezet számára. Ha azonban csak most kezdi meg az első lépéseket, érdemes megismerni a rendszergazdák, a jóváhagyó és a kérelmező általános forgatókönyveit.

## <a name="administrators"></a>A rendszergazdák

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Új vagyok a jogosultságok felügyeletére, és segítségre van szükségem az első lépésekhez

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | [Az első hozzáférési csomag létrehozásához kövesse az oktatóanyagot](entitlement-management-access-package-first.md) | [![Azure Portal ikon](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Szeretném engedélyezni a címtárban lévő felhasználóknak, hogy hozzáférést kérjenek a csoportokhoz, alkalmazásokhoz vagy SharePoint-webhelyekhez.

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Új hozzáférési csomag létrehozása katalógusban](entitlement-management-access-package-create.md#start-new-access-package) | ![Hozzáférési csomag létrehozása](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Erőforrás-Szerepkörök hozzáadása a csomaghoz való hozzáféréshez](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Csoportok</li><li>Alkalmazások</li><li>SharePoint-webhelyek</li></ul> | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Szabályzat hozzáadása](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>A címtárban lévő felhasználók számára</li><li>Jóváhagyás szükséges</li><li>Elévülési beállítások</li></ul> | ![Szabályzat hozzáadása](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Szeretném engedélyezni a felhasználók számára az üzleti partnerek címtárát (beleértve a címtárban még nem szereplő felhasználókat is), hogy hozzáférést kérjenek a csoportokhoz, alkalmazásokhoz vagy SharePoint-webhelyekhez.

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Új hozzáférési csomag létrehozása katalógusban](entitlement-management-access-package-create.md#start-new-access-package) | ![Hozzáférési csomag létrehozása](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Erőforrás-Szerepkörök hozzáadása a csomaghoz való hozzáféréshez](entitlement-management-access-package-edit.md#add-resource-roles) | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Szabályzat hozzáadása külső felhasználók számára](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>A címtárban nem szereplő felhasználók számára</li><li>Jóváhagyás szükséges</li><li>Elévülési beállítások</li></ul> | ![Házirend hozzáadása a külső felhasználók számára](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [A saját hozzáférési portál hivatkozásra kattintva küldje el a hozzáférési csomagot az üzleti partnerének](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Az üzleti partner megoszthatja a hivatkozásokat a felhasználókkal</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Egy hozzáférési csomagban szeretném módosítani a csoportokat, az alkalmazásokat és a SharePoint-helyeket

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** A hozzáférési csomag megnyitása | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Erőforrás-Szerepkörök hozzáadása vagy eltávolítása](entitlement-management-access-package-edit.md#add-resource-roles) | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Szeretném megtekinteni, hogy kik rendelkeznek hozzárendeléssel csoportokkal, alkalmazásokkal vagy SharePoint-webhelyekkel

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** Hozzáférési csomag megnyitása | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Hozzárendelések megtekintése](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Hozzáférési csomaghoz hozzáféréssel rendelkező felhasználók megtekintése</li><li>A felhasználó hozzáférésének megtekintése lejárt</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Meg szeretném tekinteni azokat a csoportokat, alkalmazásokat vagy SharePoint-webhelyeket, amelyekhez a felhasználónak hozzáférése van

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | [Felhasználói hozzárendelések jelentésének megtekintése](entitlement-management-reports.md)<ul><li>Megtekintés a kért és a jóváhagyott</li></ul> |  |

## <a name="approvers"></a>Jóváhagyók

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Szeretném jóváhagyni a csoportok, alkalmazások vagy SharePoint-webhelyek hozzáférésére vonatkozó kérelmeket

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Kérelem megnyitása a saját hozzáférési portálon](entitlement-management-request-approve.md#open-request) | [![Saját hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Hozzáférési kérelem jóváhagyása](entitlement-management-request-approve.md#approve-or-deny-request) | ![Hozzáférés jóváhagyása](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Kérelmezőket

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Szeretném megtekinteni a Microsoft számára elérhető csoportokat, alkalmazásokat vagy SharePoint-webhelyeket, és hozzáférést igényelni

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Bejelentkezés a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Saját hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Hozzáférési csomag keresése |  |
> | **3.** [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package) | ![Hozzáférés kérése](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Külső felhasználó vagyok, és szeretnék hozzáférést kérni a csoportokhoz, alkalmazásokhoz vagy SharePoint-helyekhez közvetlen kapcsolattal

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [A saját hozzáférési portál kapott hivatkozásának megkeresése](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Bejelentkezés a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Saját hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package) | ![Hozzáférés kérése külső felhasználó számára](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Szeretném megtekinteni azokat a csoportokat, alkalmazásokat vagy SharePoint-webhelyeket, amelyeknek már van hozzáférése

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Bejelentkezés a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Saját hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Aktív hozzáférési csomagok megtekintése |  |

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md)
- [Feladatok delegálása](entitlement-management-delegate.md)
