---
title: Bővítményerőforrás-típusok
description: Felsorolja az Azure-erőforrástípusok használatát a más erőforrástípusok képességeinek bővítésére.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8b80c63d361f3ad8199fd669178f7bf88dabe02e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969746"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Más erőforrások képességeit kiterjesztő erőforrástípusok

A kiterjesztési erőforrás olyan erőforrás, amely egy másik erőforrás képességeihez járul hozzá. Például az erőforrás-zárolás egy bővítmény-erőforrás. Egy erőforrás-zárolást egy másik erőforráshoz alkalmazva megakadályozhatja a törlését vagy módosítását. Nincs értelme létrehozni erőforrás-zárolást önmagában. A bővítmény erőforrásait mindig egy másik erőforrásra alkalmazza a rendszer.

## <a name="extension-resource-types"></a>Bővítményerőforrás-típusok

- Microsoft. Advisor/konfigurációk
- Microsoft. Advisor/javaslatok
- Microsoft. Advisor/tiltások
- Microsoft. AlertsManagement/riasztások
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/accessReviewScheduleDefinitions
- Microsoft. Authorization/accessReviewScheduleSettings
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/findOrphanRoleAssignments
- Microsoft. engedélyezés/zárolások
- Microsoft. Authorization/engedélyek
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleAssignmentsUsageMetrics
- Microsoft. Authorization/roleDefinitions
- Microsoft. automanage/configurationProfileAssignments
- Microsoft. Billing/billingPeriods
- Microsoft. Billing/billingPermissions
- Microsoft. Billing/billingRoleAssignments
- Microsoft. Billing/billingRoleDefinitions
- Microsoft. Billing/createBillingRoleAssignment
- Microsoft. Blueprint/blueprintAssignments
- Microsoft. Blueprint/tervrajzok
- Microsoft. ChangeAnalysis/resourceChanges
- Microsoft. fogyasztás/AggregatedCost
- Microsoft. fogyasztás/egyenlegek
- Microsoft. fogyasztás/költségvetés
- Microsoft. fogyasztás/díjak
- Microsoft. fogyasztás/CostTags
- Microsoft. felhasználás/kreditek
- Microsoft. felhasználás/események
- Microsoft. felhasználás/előrejelzések
- Microsoft. fogyasztás/tételek
- Microsoft. fogyasztás/piactér
- Microsoft. fogyasztás/OperationResults
- Microsoft. fogyasztás/OperationStatus
- Microsoft. fogyasztás/Pricesheets
- Microsoft. fogyasztás/termékek
- Microsoft. fogyasztás/ReservationDetails
- Microsoft. fogyasztás/ReservationRecommendationDetails
- Microsoft. fogyasztás/ReservationRecommendations
- Microsoft. fogyasztás/ReservationSummaries
- Microsoft. fogyasztás/ReservationTransactions
- Microsoft. felhasználás/Címkék
- Microsoft. fogyasztás/bérlők
- Microsoft. felhasználás/feltételek
- Microsoft. fogyasztás/UsageDetails
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/riasztások
- Microsoft. CostManagement/költségvetés
- Microsoft. CostManagement/costAllocationRules
- Microsoft. CostManagement/méretek
- Microsoft. CostManagement/export
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/előrejelzés
- Microsoft. CostManagement/bepillantások
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/jelentések
- Microsoft. CostManagement/showbackRules
- Microsoft. CostManagement/nézetek
- Microsoft. CustomProviders/társítások
- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/szoftver
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. bepillantások/alapterv
- Microsoft. bepillantások/calculatebaseline
- Microsoft. bepillantások/dataCollectionRuleAssociations
- Microsoft. bepillantások/diagnosticSettings
- Microsoft. bepillantások/diagnosticSettingsCategories
- Microsoft. bepillantások/eventtypes
- Microsoft. bepillantások/extendedDiagnosticSettings
- Microsoft. bepillantások/generateLiveToken
- Microsoft. bepillantások/guestDiagnosticSettingsAssociation
- Microsoft. bepillantások/logDefinitions
- Microsoft. elemzések/naplók
- Microsoft. bepillantások/metricbaselines
- Microsoft. bepillantások/metricDefinitions
- Microsoft. bepillantások/metricNamespaces
- Microsoft. bepillantások/mérőszámok
- Microsoft. bepillantások/myWorkbooks
- Microsoft. bepillantások/topológia
- Microsoft. bepillantások/tranzakciók
- Microsoft. bepillantások/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/bővítmények
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates
- Microsoft. ManagedIdentity/identitások
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/igazolások
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/szervizelések
- Microsoft. Recoveryservices szolgáltatónál/backupProtectedItems
- Microsoft. Recoveryservices szolgáltatónál/replicationEligibilityResults
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/események
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/értesítések
- Microsoft. Resources/hivatkozások
- Microsoft. Resources/Tags
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/értékelések
- Microsoft. Security/complianceResults
- Microsoft. Security/megfelelőség
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments
- Microsoft. SecurityInsights/összesítések
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/könyvjelzők
- Microsoft. SecurityInsights/esetek
- Microsoft. SecurityInsights/dataConnectors
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/entitások
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/incidensek
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/beállítások
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/listák
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. előfizetés/CreateSubscription
- Microsoft. support/supporttickets
- Microsoft. WorkloadMonitor/összetevők
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/figyelők
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Következő lépések

- Ha egy Azure Resource Manager-sablonban szeretné lekérni a bővítmény erőforrásának erőforrás-AZONOSÍTÓját, használja a [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- A bővítmény-erőforrások sablonban való létrehozásával kapcsolatos példát a [Event Grid esemény-előfizetések](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)című témakörben talál.
