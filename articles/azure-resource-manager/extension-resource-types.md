---
title: Azure-bővítmények erőforrástípusok
description: Felsorolja az Azure-erőforrástípusok használatát a más erőforrástípusok képességeinek bővítésére.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 00e8efaaa81ec0ce189d8cfe8a78285ded445c68
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901923"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Más erőforrások képességeit kiterjesztő erőforrástípusok

A kiterjesztési erőforrás olyan erőforrás, amely egy másik erőforrás képességeihez járul hozzá. Például az erőforrás-zárolás egy bővítmény-erőforrás. Egy erőforrás-zárolást egy másik erőforráshoz alkalmazva megakadályozhatja a törlését vagy módosítását. Nincs értelme létrehozni erőforrás-zárolást önmagában. A bővítmény erőforrásait mindig egy másik erőforrásra alkalmazza a rendszer.

## <a name="extension-resource-types"></a>Kiterjesztési erőforrástípusok

- Microsoft. Advisor/konfigurációk
- Microsoft. Advisor/javaslatok
- Microsoft. Advisor/tiltások
- Microsoft. AlertsManagement/riasztások
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. engedélyezés/zárolások
- Microsoft. Authorization/engedélyek
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions
- Microsoft. Billing/billingPeriods
- Microsoft. Billing/billingPermissions
- Microsoft. Billing/billingRoleAssignments
- Microsoft. Billing/billingRoleDefinitions
- Microsoft. Billing/createBillingRoleAssignment
- Microsoft. Blueprint/blueprintAssignments
- Microsoft. Blueprint/tervrajzok
- Microsoft. fogyasztás/AggregatedCost
- Microsoft. fogyasztás/egyenlegek
- Microsoft. fogyasztás/költségvetés
- Microsoft. fogyasztás/díjak
- Microsoft. fogyasztás/CostTags
- Microsoft. felhasználás/előrejelzések
- Microsoft. fogyasztás/piactér
- Microsoft. fogyasztás/OperationResults
- Microsoft. fogyasztás/OperationStatus
- Microsoft. fogyasztás/Pricesheets
- Microsoft. fogyasztás/ReservationDetails
- Microsoft. fogyasztás/ReservationRecommendations
- Microsoft. fogyasztás/ReservationSummaries
- Microsoft. fogyasztás/ReservationTransactions
- Microsoft. felhasználás/Címkék
- Microsoft. felhasználás/feltételek
- Microsoft. fogyasztás/UsageDetails
- Microsoft. felhasználás/kreditek
- Microsoft. felhasználás/események
- Microsoft. fogyasztás/tételek
- Microsoft. fogyasztás/termékek
- Microsoft. fogyasztás/bérlők
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/riasztások
- Microsoft. CostManagement/költségvetés
- Microsoft. CostManagement/méretek
- Microsoft. CostManagement/export
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/előrejelzés
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/jelentések
- Microsoft. CostManagement/nézetek
- Microsoft. CostManagement/showbackRules
- Microsoft. CustomProviders/társítások
- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/szoftver
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. bepillantások/automatedExportSettings
- Microsoft. bepillantások/alapterv
- Microsoft. bepillantások/calculatebaseline
- Microsoft. bepillantások/diagnosticSettings
- Microsoft. bepillantások/diagnosticSettingsCategories
- Microsoft. bepillantások/eventtypes
- Microsoft. bepillantások/extendedDiagnosticSettings
- Microsoft. bepillantások/guestDiagnosticSettingsAssociation
- Microsoft. bepillantások/logDefinitions
- Microsoft. elemzések/naplók
- Microsoft. bepillantások/metricDefinitions
- Microsoft. bepillantások/metricNamespaces
- Microsoft. bepillantások/metricbaselines
- Microsoft. bepillantások/mérőszámok
- Microsoft. bepillantások/myWorkbooks
- Microsoft. bepillantások/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates
- Microsoft. ManagedIdentity/identitások
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/szervizelések
- Microsoft. Recoveryservices szolgáltatónál/backupProtectedItems
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/események
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/értesítések
- Microsoft. Resources/hivatkozások
- Microsoft. Resources/Tags
- Microsoft. Security/megfelelőség
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/értékelések
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/dataCollectionResults
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/networkData
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. SecurityInsights/összesítések
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/könyvjelzők
- Microsoft. SecurityInsights/esetek
- Microsoft. SecurityInsights/dataConnectors
- Microsoft. SecurityInsights/entitások
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/beállítások
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. előfizetés/CreateSubscription
- Microsoft. support/createsupportticket
- Microsoft. support/supporttickets
- Microsoft. WorkloadMonitor/összetevők
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/figyelők
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Következő lépések

- Ha egy Azure Resource Manager-sablonban szeretné lekérni a bővítmény erőforrásának erőforrás-AZONOSÍTÓját, használja a [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid).
- A bővítmény-erőforrások sablonban való létrehozásával kapcsolatos példát a [Event Grid esemény-előfizetések](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)című témakörben talál.
