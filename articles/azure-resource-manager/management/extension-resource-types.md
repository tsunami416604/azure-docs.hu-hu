---
title: Bővítményerőforrás-típusok
description: Felsorolja az Azure-erőforrás-típusok más erőforrástípusok képességeinek bővítésére használt.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3a3fbc531750bec4b16e38f1fe79f613c1b94f5e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754868"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Más erőforrások képességeit bővítő erőforrástípusok

A bővítményerőforrás olyan erőforrás, amely hozzáadja egy másik erőforrás képességeit. Például az erőforrászárolás egy bővítményerőforrás. Erőforrászárolást alkalmazhat egy másik erőforrásra, hogy megakadályozza annak törlését vagy módosítását. Nincs értelme önmagában erőforrás-zárolást létrehozni. A bővítményerőforrás mindig egy másik erőforrásra lesz alkalmazva.

## <a name="extension-resource-types"></a>Bővítményerőforrás-típusok

- Microsoft.Advisor/configurations
- Microsoft.Advisor/recommendations
- Microsoft.Advisor/elnyomás
- Microsoft.AlertsManagement/alerts
- Microsoft.AlertsManagement/alertsÖsszefoglaló
- Microsoft.Authorization/checkAccess
- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Engedélyezési/zárolások
- Microsoft.Authorization/permissions
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleAssignmentsUsageMetrics
- Microsoft.Authorization/roleDefinitions
- Microsoft.Billing/billingIdőszakok
- Microsoft.Billing/billingEngedélyek
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints
- Microsoft.Consumption/AggregatedCost
- Microsoft.Fogyasztás/egyenlegek
- Microsoft.Fogyasztás/költségvetés
- Microsoft.Felhasználás/díjak
- Microsoft.Fogyasztás/Költségcímkék
- Microsoft.Fogyasztás/előrejelzések
- Microsoft.Fogyasztás/piacterek
- Microsoft.Consumption/OperationResults
- Microsoft.Felhasználás/Műveletállapota
- Microsoft.Fogyasztás/Árlapok
- Microsoft.Felhasználás/FoglalásRészletek
- Microsoft.Consumption/ReservationRecommendations Microsoft.Consumption/ReservationRecommendations Microsoft.Consumption/Reservation
- Microsoft.Fogyasztás/ReservationSummaries
- Microsoft.Felhasználás/foglalási tranzakciók
- Microsoft.Fogyasztás/Címkék
- Microsoft.Felhasználás/feltételek
- Microsoft.Felhasználás/HasználatRészletek
- Microsoft.Felhasználás/kreditek
- Microsoft.Fogyasztás/események
- Microsoft.Fogyasztás/tételek
- Microsoft.Fogyasztás/termékek
- Microsoft.Fogyasztás/bérlők
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Figyelmeztetések
- Microsoft.CostManagement/Budgets
- Microsoft.CostManagement/Dimenziók
- Microsoft.CostManagement/Export
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Előrejelzés
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/Views
- Microsoft.CostManagement/showbackRules
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTémakörök
- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestKonfiguráció/szoftver
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestKonfiguráció/szoftverfrissítések
- microsoft.insights/baseline
- microsoft.insights/calculatebaseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticBeállítások
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topológia
- microsoft.insights/transactions
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesKonfiguráció/forrásControlConfigurations
- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments
- Microsoft.Karbantartás/frissítések
- Microsoft.ManagedIdentity/Identitások
- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsManagement/managementassociations
- Microsoft.PolicyInsights/policyEsemények
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults    
- Microsoft.ResourceHealth/availabilityStatuses
- Microsoft.ResourceHealth/childAvailabilityStatuses
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/események
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/értesítések
- Microsoft.Resources/links
- Microsoft.Resources/tags
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments microsoft.Security/assessments
- Microsoft.Security/complianceEredmények
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverBiztonsági résértékelése
- Microsoft.SecurityInsights/aggregations
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/könyvjelzők
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/entitások
- Microsoft.SecurityInsights/entityQueries
- Microsoft.SecurityInsights/Incidents
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/settings
- Microsoft.SoftwarePlan/hybridElőnyök
- Microsoft.Subscription/CreateSubscription
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/összetevők
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitorok
- Microsoft.WorkloadMonitor/notificationBeállítások

## <a name="next-steps"></a>További lépések

- Egy bővítményerőforrás-azonosító lekérni egy Azure Resource Manager-sablonban az [ExtensionResourceId .To](../templates/template-functions-resource.md#extensionresourceid)get the resource id for the resource id for the resource id.
- Ha például egy bővítményerőforrást hoz létre egy sablonban, olvassa el az [Eseményrács esemény- előfizetései című témakört.](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)
