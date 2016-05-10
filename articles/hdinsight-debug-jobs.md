<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Debug Hadoop in HDInsight: Error messages | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Debug Hadoop in HDInsight: Error messages

## Introduction

The error messages itemized in this topic are provided to help the users of Hadoop in Azure HDInsight understand possible error conditions that they can encounter when administering the service using Azure PowerShell and to advise them on the steps which can be taken to recover from the error.

Some of these error messages could also be seen in the Azure portal when it is used to manage HDInsight clusters. But other error messages you might encounter there are less granular due to the constraints on the remedial actions possible in this context. Other error messages are provided in the contexts where the mitigation is obvious. If the constraints on parameters are violated, for example, the message pops-up in on the right side of the box where the value was entered. Here is a case where too many data nodes have been requested. The remedy is to reduce the number to an allowed value that is 22 or less.

![HDI.Debugging.ErrorMessages.Portal][HDI.Debugging.ErrorMessages.Portal]

The errors a user can encounter in Azure PowerShell or in the Azure Portal are listed alphabetically by name in the [HDInsight Errors][HDInsight Errors] section where they are linked to an entry in the [Discription and Mitigation of Errors][Discription and Mitigation of Errors] section that provide the following information for the error:

-   **Description**: the error message users see
-   **Mitigation**: what steps can be taken to recover from the error.

### HDInsight Errors

[AtleastOneSqlMetastoreMustBeProvided][AtleastOneSqlMetastoreMustBeProvided]
[AzureRegionNotSupported][AzureRegionNotSupported]
[ClusterContainerRecordNotFound][ClusterContainerRecordNotFound]
[ClusterDnsNameInvalidReservedWord][ClusterDnsNameInvalidReservedWord]
[ClusterNameUnavailable][ClusterNameUnavailable]
[ClusterUserNameInvalid][ClusterUserNameInvalid]
[ClusterUserNameInvalidReservedWord][ClusterUserNameInvalidReservedWord]
[ContainerNameMisMatchWithDnsName][ContainerNameMisMatchWithDnsName]
[DataNodeDefinitionNotFound][DataNodeDefinitionNotFound]
[DeploymentDeletionFailure][DeploymentDeletionFailure]
[DnsMappingNotFound][DnsMappingNotFound]
[DuplicateClusterContainerRequest][DuplicateClusterContainerRequest]
[DuplicateClusterInHostedService][DuplicateClusterInHostedService]
[FailureToUpdateDeploymentStatus][FailureToUpdateDeploymentStatus]
[HdiRestoreClusterAltered][HdiRestoreClusterAltered]
[HeadNodeConfigNotFound][HeadNodeConfigNotFound]
[HeadNodeConfigNotFound][HeadNodeConfigNotFound]
[HostedServiceCreationFailure][HostedServiceCreationFailure]
[HostedServiceHasProductionDeployment][HostedServiceHasProductionDeployment]
[HostedServiceNotFound][HostedServiceNotFound]
[HostedServiceWithNoDeployment][HostedServiceWithNoDeployment]
[InsufficientResourcesCores][InsufficientResourcesCores]
[InsufficientResourcesHostedServices][InsufficientResourcesHostedServices]
[InternalErrorRetryRequest][InternalErrorRetryRequest]
[InvalidAzureStorageLocation][InvalidAzureStorageLocation]
[InvalidNodeSizeForDataNode][InvalidNodeSizeForDataNode]
[InvalidNodeSizeForHeadNode][InvalidNodeSizeForHeadNode]
[InvalidRightsForDeploymentDeletion][InvalidRightsForDeploymentDeletion]
[InvalidStorageAccountBlobContainerName][InvalidStorageAccountBlobContainerName]
[InvalidStorageAccountConfigurationSecretKey][InvalidStorageAccountConfigurationSecretKey]
[InvalidVersionHeaderFormat][InvalidVersionHeaderFormat]
[MoreThanOneHeadNode][MoreThanOneHeadNode]
[OperationTimedOutRetryRequest][OperationTimedOutRetryRequest]
[ParameterNullOrEmpty][ParameterNullOrEmpty]
[PreClusterCreationValidationFailure][PreClusterCreationValidationFailure]
[RegionCapabilityNotAvailable][RegionCapabilityNotAvailable]
[StorageAccountNotColocated][StorageAccountNotColocated]
[SubscriptionIdNotActive][SubscriptionIdNotActive]
[SubscriptionIdNotFound][SubscriptionIdNotFound]
[UnableToResolveDNS][UnableToResolveDNS]
[UnableToVerifyLocationOfResource][UnableToVerifyLocationOfResource]
[VersionCapabilityNotAvailable][VersionCapabilityNotAvailable]
[VersionNotSupported][VersionNotSupported]
[VersionNotSupportedInRegion][VersionNotSupportedInRegion]
[WasbAccountConfigNotFound][WasbAccountConfigNotFound]

## <span id="discription-mitigation-errors"></span></a>Diagnosis and Mitigation of Errors

### <span id="AtleastOneSqlMetastoreMustBeProvided"></span></a>AtleastOneSqlMetastoreMustBeProvided

-   **Description**: Please provide Azure SQL database details for at least one component in order to use custom settings for Hive and Oozie metastores.
-   **Mitigation**: The user needs to supply a valid SQL Azure metastore and retry the request.

### <span id="AzureRegionNotSupported"></span></a>AzureRegionNotSupported

-   **Description**: Could not create cluster in region *nameOfYourRegion*. Use a valid HDInsight region and retry request.
-   **Mitigation**: Customer should create the cluster region that currently supports them: Southeast Asia, West Europe, North Europe, East US, or West US.

### <span id="ClusterContainerRecordNotFound"></span></a>ClusterContainerRecordNotFound

-   **Description**: The server could not find the requested cluster record.
-   **Mitigation**: Retry the operation.

### <span id="ClusterDnsNameInvalidReservedWord"></span></a>ClusterDnsNameInvalidReservedWord

-   **Description**: Cluster DNS name *yourDnsName* is invalid. Please ensure name starts and ends with alphanumeric and can only contain '-' special character
-   **Mitigation**: Make sure that you have used a valid DNS name for your cluster that starts and ends with alphanumeric and contains no special characters other than the dash '-' and then retry the operation.

### <span id="ClusterNameUnavailable"></span></a>ClusterNameUnavailable

-   **Description**: Cluster name *yourClusterName* is unavailable. Please pick another name.
-   **Mitigation**: The user should specify a clustername that is unique and does not exist and retry. If the user is using the portal, the UI will notify them if a cluster name is already being used during the create steps.

### <span id="ClusterPasswordInvalid"></span></a>ClusterPasswordInvalid

-   **Description**: Cluster password is invalid. Password must be at least 10 characters long and must contain at least one number, uppercase letter, lowercase letter and special character with no spaces and should not contain the username as part of it.
-   **Mitigation**: Provide a valid cluster password and retry the operation.

### <span id="ClusterUserNameInvalid"></span></a>ClusterUserNameInvalid

-   **Description**: Cluster username is invalid. Please ensure username doesn't contain special characters or spaces.
-   **Mitigation**: Provide a valid cluster username and retry the operation.

### <span id="ClusterUserNameInvalidReservedWord"></span></a>ClusterUserNameInvalidReservedWord

-   **Description**: Cluster DNS name *yourDnsClusterName* is invalid. Please ensure name starts and ends with alphanumeric and can only contain '-' special character
-   **Mitigation**: Provide a valid DNS cluster username and retry the operation.

### <span id="ContainerNameMisMatchWithDnsName"></span></a>ContainerNameMisMatchWithDnsName

-   **Description**: Container name in URI *yourcontainerURI* and DNS name *yourDnsName* in request body must be the same.
-   **Mitigation**: Make sure that your container Name and your DNS name are the same and retry the operation.

### <span id="DataNodeDefinitionNotFound"></span></a>DataNodeDefinitionNotFound

-   **Description**: Invalid cluster configuration. Unable to find any data node definitions in node size.
-   **Mitigation**: Retry the operation.

### <span id="DeploymentDeletionFailure"></span></a>DeploymentDeletionFailure

-   **Description**: Deletion of deployment failed for the Cluster
-   **Mitigation**: Retry the delete operation.

### <span id="DnsMappingNotFound"></span></a>DnsMappingNotFound

-   **Description**: Service configuration error. Required DNS mapping information not found.
-   **Mitigation**: Delete cluster and create a new cluster.

### <span id="DuplicateClusterContainerRequest"></span></a>DuplicateClusterContainerRequest

-   **Description**: Duplicate cluster container creation attempt. Record exists for *nameOfYourContainer* but Etags do not match.
-   **Mitigation**: Provide a unique name for the container and retry the create operation.

### <span id="DuplicateClusterInHostedService"></span></a>DuplicateClusterInHostedService

-   **Description**: Hosted service *nameOfYourHostedService* already contains a cluster. A hosted service cannot contain multiple clusters
-   **Mitigation**: Host the cluster in another hosted service.

### <span id="FailureToUpdateDeploymentStatus"></span></a>FailureToUpdateDeploymentStatus

-   **Description**: The server could not update the state of the cluster deployment.
-   **Mitigation**: Retry the operation. If this happens multiple times, contact CSS.

### <span id="HdiRestoreClusterAltered"></span></a>HdiRestoreClusterAltered

-   **Description**: Cluster *yourClusterName* was deleted as part of maintenance. Please recreate the cluster.
-   **Mitigation**: Recreate the cluster.

### <span id="HeadNodeConfigNotFound"></span></a>HeadNodeConfigNotFound

-   **Description**: Invalid cluster configuration. Required head node configuration not found in node sizes.
-   **Mitigation**: Retry the operation.

### <span id="HostedServiceCreationFailure"></span></a>HostedServiceCreationFailure

-   **Description**: Unable to create hosted service *nameOfYourHostedService*. Please retry request.
-   **Mitigation**: Retry the request.

### <span id="HostedServiceHasProductionDeployment"></span></a>HostedServiceHasProductionDeployment

-   **Description**: Hosted Service *nameOfYourHostedService* already has a production deployment. A hosted service cannot contain multiple production deployments. Retry the request with a different cluster name.
-   **Mitigation**: Use a different cluster name and retry the request.

### <span id="HostedServiceNotFound"></span></a>HostedServiceNotFound

-   **Description**: Hosted Service *nameOfYourHostedService* for the cluster could not be found.
-   **Mitigation**: If the cluster is in error state, delete it and then try again.

### <span id="HostedServiceWithNoDeployment"></span></a>HostedServiceWithNoDeployment

-   **Description**: Hosted Service *nameOfYourHostedService* has no associated deployment.
-   **Mitigation**: If the cluster is in error state, delete it and then try again.

### <span id="InsufficientResourcesCores"></span></a>InsufficientResourcesCores

-   **Description**: The SubscriptionId *yourSubscriptionId* does not have cores left to create cluster *yourClusterName*. Required: *resourcesRequired*, Available: *resourcesAvailable*.
-   **Mitigation**: Free up resources in your subscription or increase the resources available to the subscription and try to create the cluster again.

### <span id="InsufficientResourcesHostedServices"></span></a>InsufficientResourcesHostedServices

-   **Description**: Subscription ID *yourSubscriptionId* does not have quota for a new HostedService to create cluster *yourClusterName*.
-   **Mitigation**: Free up resources in your subscription or increase the resources available to the subscription and try to create the cluster again.

### <span id="InternalErrorRetryRequest"></span></a>InternalErrorRetryRequest

-   **Description**: The server encountered an internal error. Please retry request.
-   **Mitigation**: Retry the request.

### <span id="InvalidAzureStorageLocation"></span></a>InvalidAzureStorageLocation

-   **Description**: Azure Storage location *dataRegionName* is not a valid location. Make sure the region is correct and retry request.
-   **Mitigation**: Select a Storage location that supports HDInsight, check that your cluster is co-located and retry the operation.

### <span id="InvalidNodeSizeForDataNode"></span></a>InvalidNodeSizeForDataNode

-   **Description**: Invalid VM size for data nodes. Only 'Large VM' size is supported for all data nodes.
-   **Mitigation**: Specify the supported node size for the data node and retry the operation.

### <span id="InvalidNodeSizeForHeadNode"></span></a>InvalidNodeSizeForHeadNode

-   **Description**: Invalid VM size for head node. Only 'ExtraLarge VM' size is supported for head node.
-   **Mitigation**: Specify the supported node size for the head node and retry the operation

### <span id="InvalidRightsForDeploymentDeletion"></span></a>InvalidRightsForDeploymentDeletion

-   **Description**: Subscription ID *yourSubscriptionId* being used does not have sufficient permissions to execute delete operation for cluster *yourClusterName*.
-   **Mitigation**: If the cluster is in error state, drop it and then try again.

### <span id="InvalidStorageAccountBlobContainerName"></span></a>InvalidStorageAccountBlobContainerName

-   **Description**: External storage account blob container name *yourContainerName* is invalid. Make sure name starts with a letter and contains only lowercase letters, numbers and dash.
-   **Mitigation**: Specify a valid storage account blob container name and retry the operation.

### <span id="InvalidStorageAccountConfigurationSecretKey"></span></a>InvalidStorageAccountConfigurationSecretKey

-   **Description**: Configuration for external storage account *yourStorageAccountName* is required to have secret key details to be set.
-   **Mitigation**: Specify a valid secret key for the storage account and retry the operation.

### <span id="InvalidVersionHeaderFormat"></span></a>InvalidVersionHeaderFormat

-   **Description**: Version header *yourVersionHeader* is not in valid format of yyyy-mm-dd.
-   **Mitigation**: Specify a valid format for the version-header and retry the request.

### <span id="MoreThanOneHeadNode"></span></a>MoreThanOneHeadNode

-   **Description**: Invalid cluster configuration. Found more than one head node configuration.
-   **Mitigation**: Edit the configuration so that onloy one head node is specified.

### <span id="OperationTimedOutRetryRequest"></span></a>OperationTimedOutRetryRequest

-   **Description**: The operation could not be completed within the permitted time or the maximum retry attempts possible. Please retry request.
-   **Mitigation**: Retry the request.

### <span id="ParameterNullOrEmpty"></span></a>ParameterNullOrEmpty

-   **Description**: Parameter *yourParameterName* cannot be null or empty.
-   **Mitigation**: Specify a valid value for the parameter.

### <span id="PreClusterCreationValidationFailure"></span></a>PreClusterCreationValidationFailure

-   **Description**: One or more of the cluster creation request inputs is not valid. Make sure the input values are correct and retry request.
-   **Mitigation**: Make sure the input values are correct and retry request.

### <span id="RegionCapabilityNotAvailable"></span></a>RegionCapabilityNotAvailable

-   **Description**: Region capability not available for region *yourRegionName* and Subscription ID *yourSubscriptionId*.
-   **Mitigation**: Specify a region that supports HDInsight clusters. The publicly supported regions are: Southeast Asia, West Europe, North Europe, East US, or West US.

### <span id="StorageAccountNotColocated"></span></a>StorageAccountNotColocated

-   **Description**: Storage account *yourStorageAccountName* is in region *currentRegionName*. It should be same as the cluster region *yourClusterRegionName*.
-   **Mitigation**: Either specify a storage account in the same region that your cluster is in or if your data is already in the storage account, create a new cluster in the same region as the existing storage account. If you are using the portal, the UI will notify them of this issue in advance.

### <span id="SubscriptionIdNotActive"></span></a>SubscriptionIdNotActive

-   **Description**: Given Subscription ID *yourSubscriptionId* is not active.
-   **Mitigation**: Re-activate your subscription or get a new valid subscription.

### <span id="SubscriptionIdNotFound"></span></a>SubscriptionIdNotFound

-   **Description**: Subscription ID *yourSubscriptionId* could not be found.
-   **Mitigation**: Check that your subscription ID is valid and retry the operation.

### <span id="UnableToResolveDNS"></span></a>UnableToResolveDNS

-   **Description**: Unable to resolve DNS *yourDnsUrl*. Please ensure the fully qualified URL for the blob endpoint is provided.
-   **Mitigation**: Supply a valid blob URL. The URL MUST be fully valid, including starting with *<http://>* and ending in *.com*. The fully qualified URL can usually be found in the storage tab of the manage.windowsazure.com portal.

### <span id="UnableToVerifyLocationOfResource"></span></a>UnableToVerifyLocationOfResource

-   **Description**: Unable to verify location of resource *yourDnsUrl*. Please ensure the fully qualified URL for the blob endpoint is provided.
-   **Mitigation**: Supply a valid blob URL. The URL MUST be fully valid, including starting with *<http://>* and ending in *.com*. The fully qualified URL can usually be found in the storage tab of the manage.windowsazure.com portal.

### <span id="VersionCapabilityNotAvailable"></span></a>VersionCapabilityNotAvailable

-   **Description**: Version capability not available for version *specifiedVersion* and Subscription ID *yourSubscriptionId*.
-   **Mitigation**: Choose a version that is available and retry the operation.

### <span id="VersionNotSupported"></span></a>VersionNotSupported

-   **Description**: Version *specifiedVersion* not supported.
-   **Mitigation**: Choose a version that is supported and retry the operation.

### <span id="VersionNotSupportedInRegion"></span></a>VersionNotSupportedInRegion

-   **Description**: Version *specifiedVersion* is not available in Azure region *specifiedRegion*.
-   **Mitigation**: Choose a version that is supported in the region specified and retry the operation.

### <span id="WasbAccountConfigNotFound"></span></a>WasbAccountConfigNotFound

-   **Description**: Invalid cluster configuration. Required WASB account configuration not found in external accounts.
-   **Mitigation**: Verify that the account exists and is properly specified in configuration and retry the operation.

## <span id="resources"></span></a>Additional Debugging Resources

-   [Azure HDInsight SDK documentation][Azure HDInsight SDK documentation]

  [HDI.Debugging.ErrorMessages.Portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png
  [HDInsight Errors]: #hdinsight-error-messages
  [Discription and Mitigation of Errors]: #discription-mitigation-errors
  [AtleastOneSqlMetastoreMustBeProvided]: #AtleastOneSqlMetastoreMustBeProvided
  [AzureRegionNotSupported]: #AzureRegionNotSupported
  [ClusterContainerRecordNotFound]: #ClusterContainerRecordNotFound
  [ClusterDnsNameInvalidReservedWord]: #ClusterDnsNameInvalidReservedWord
  [ClusterNameUnavailable]: #ClusterNameUnavailable
  [ClusterUserNameInvalid]: #ClusterUserNameInvalid
  [ClusterUserNameInvalidReservedWord]: #ClusterUserNameInvalidReservedWord
  [ContainerNameMisMatchWithDnsName]: #ContainerNameMisMatchWithDnsName
  [DataNodeDefinitionNotFound]: #DataNodeDefinitionNotFound
  [DeploymentDeletionFailure]: #DeploymentDeletionFailure
  [DnsMappingNotFound]: #DnsMappingNotFound
  [DuplicateClusterContainerRequest]: #DuplicateClusterContainerRequest
  [DuplicateClusterInHostedService]: #DuplicateClusterInHostedService
  [FailureToUpdateDeploymentStatus]: #FailureToUpdateDeploymentStatus
  [HdiRestoreClusterAltered]: #HdiRestoreClusterAltered
  [HeadNodeConfigNotFound]: #HeadNodeConfigNotFound
  [HostedServiceCreationFailure]: #HostedServiceCreationFailure
  [HostedServiceHasProductionDeployment]: #HostedServiceHasProductionDeployment
  [HostedServiceNotFound]: #HostedServiceNotFound
  [HostedServiceWithNoDeployment]: #HostedServiceWithNoDeployment
  [InsufficientResourcesCores]: #InsufficientResourcesCores
  [InsufficientResourcesHostedServices]: #InsufficientResourcesHostedServices
  [InternalErrorRetryRequest]: #InternalErrorRetryRequest
  [InvalidAzureStorageLocation]: #InvalidAzureStorageLocation
  [InvalidNodeSizeForDataNode]: #InvalidNodeSizeForDataNode
  [InvalidNodeSizeForHeadNode]: #InvalidNodeSizeForHeadNode
  [InvalidRightsForDeploymentDeletion]: #InvalidRightsForDeploymentDeletion
  [InvalidStorageAccountBlobContainerName]: #InvalidStorageAccountBlobContainerName
  [InvalidStorageAccountConfigurationSecretKey]: #InvalidStorageAccountConfigurationSecretKey
  [InvalidVersionHeaderFormat]: #InvalidVersionHeaderFormat
  [MoreThanOneHeadNode]: #MoreThanOneHeadNode
  [OperationTimedOutRetryRequest]: #OperationTimedOutRetryRequest
  [ParameterNullOrEmpty]: #ParameterNullOrEmpty
  [PreClusterCreationValidationFailure]: #PreClusterCreationValidationFailure
  [RegionCapabilityNotAvailable]: #RegionCapabilityNotAvailable
  [StorageAccountNotColocated]: #StorageAccountNotColocated
  [SubscriptionIdNotActive]: #SubscriptionIdNotActive
  [SubscriptionIdNotFound]: #SubscriptionIdNotFound
  [UnableToResolveDNS]: #UnableToResolveDNS
  [UnableToVerifyLocationOfResource]: #UnableToVerifyLocationOfResource
  [VersionCapabilityNotAvailable]: #VersionCapabilityNotAvailable
  [VersionNotSupported]: #VersionNotSupported
  [VersionNotSupportedInRegion]: #VersionNotSupportedInRegion
  [WasbAccountConfigNotFound]: #WasbAccountConfigNotFound
  [Azure HDInsight SDK documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
