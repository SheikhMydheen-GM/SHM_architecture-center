This article describes an architecture that you can use to replace the manual analysis of video footage with an automated, and frequently more accurate, machine learning process.

*The FFmpeg and Jupyter Notebook logos are trademarks of their respective companies. No endorsement is implied by the use of these marks.*

## Architecture

:::image type="content" source="_images/analyze-video-content.png" alt-text="Diagram that shows an architecture for analyzing video content." lightbox="_images/analyze-video-content.png":::

*Download a [PowerPoint file](https://arch-center.azureedge.net/analyze-video-content.pptx) of this architecture.*

### Workflow

1. A collection of video footage, in MP4 format, is uploaded to Azure Blob Storage. Ideally, the videos go into a "raw" container.
2. A preconfigured pipeline in Azure Machine Learning recognizes that video files are uploaded to the container and initiates an inference cluster to start separating the video footage into frames.
3. FFmpeg, an open-source tool, breaks down the video and extracts frames. You can configure how many frames per second are extracted, the quality of the extraction, and the format of the image file. The format can be JPG or PNG.
4. The inference cluster sends the images to Azure Data Lake Storage.
5. A preconfigured logic app that monitors Data Lake Storage detects that new images are being uploaded. It starts a workflow.
6. The logic app calls a pretrained custom vision model to identify objects, features, or qualities in the images. Alternatively or additionally, it calls a computer vision (optical character recognition (OCR)) model to identify textual information in the images.
7. Results are received in JSON format. The logic app parses the results and creates key-value pairs. You can store the results in Azure dedicated SQL pools that are provisioned by Azure Synapse Analytics.
8. Power BI provides data visualization.

### Components

- [Azure Blob Storage](/azure/well-architected/service-guides/azure-blob-storage) provides object storage for cloud-native workloads and machine learning stores. In this architecture, it stores the uploaded video files.
- [Azure Machine Learning](/azure/well-architected/service-guides/azure-machine-learning) is an enterprise-grade machine learning service for the end-to-end machine learning lifecycle.
- [Azure Data Lake Storage](/azure/storage/blobs/data-lake-storage-introduction) provides massively scalable, enhanced-security, cost-effective cloud storage for high-performance analytics workloads.
- [Computer Vision](/azure/ai-services/computer-vision/overview) is part of [Azure AI services](/azure/ai-services/what-are-ai-services). It's used to retrieve information about each image.
- [Custom Vision](/azure/ai-services/custom-vision-service/overview) enables you to customize and embed state-of-the-art computer vision image analysis for your specific domains.
- [Azure Logic Apps](/azure/logic-apps/logic-apps-overview) automates workflows by connecting apps and data across environments. It provides a way to access and process data in real time.
- [Azure Synapse Analytics](/azure/synapse-analytics/overview-what-is) is a limitless analytics service that brings together data integration, enterprise data warehousing, and big data analytics.
- [Dedicated SQL pool](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is) (formerly SQL DW) is a collection of analytics resources that are provisioned when you use Azure Synapse SQL.
- [Power BI](/power-bi/fundamentals/power-bi-overview) is a collection of software services, apps, and connectors that work together to provide visualizations of your data.

### Alternatives

- [Azure Video Indexer](/azure/azure-video-indexer/video-indexer-overview) is a video analytics service that uses AI to extract actionable insights from stored videos. You can use it without any expertise in machine learning.
- [Azure Data Factory](/azure/data-factory/introduction) is a fully managed serverless data integration service that helps you construct extract, transform, load (ETL) and extract, load, transform (ELT) processes.
- [Azure Functions](/azure/azure-functions/functions-overview) is a serverless platform as a service (PaaS) that runs single-task code without requiring new infrastructure.
- [Azure Cosmos DB](/azure/well-architected/service-guides/cosmos-db) is a fully managed NoSQL database for modern app development.

## Scenario details

Many industries record video footage to detect the presence or absence of a particular object or entity or to classify objects or entities. Video monitoring and analyses are traditionally performed manually. These processes are often monotonous and prone to errors, particularly for tasks that are difficult for the human eye. You can automate these processes by using AI and machine learning.

A video recording can be separated into individual frames so that various technologies can analyze the images. One such technology is *computer vision*: the capability of a computer to identify objects and entities on an image.

With computer vision, monitoring video footage becomes automatized, standardized, and potentially more accurate. A computer vision model can be trained, and, depending on the use case, you can frequently get results that are at least as good as those of the person who trained the model. By using [Machine Learning Operations (MLOps)](/azure/machine-learning/concept-model-management-and-deployment) to improve the model continuously, you can expect better results over time, and react to changes in the video data over time.

### Potential use cases

This scenario is relevant for any business that analyzes videos. Here are some sample use cases:

- **Agriculture.** Monitor and analyze crops and soil conditions over time. By using drones or UAVs, farmers can record video footage for analysis.

- **Environmental sciences.** Analyze aquatic species to understand where they're located and how they evolve. By attaching underwater cameras to boats, environmental researchers can navigate the shoreline to record video footage. They can analyze the video footage to understand species migrations and how species populations change over time.

- **Traffic control.** Classify vehicles into categories (SUV, car, truck, motorcycle), and use the information to plan traffic control. Video footage can be provided by CCTV in public locations. Most CCTV cameras record date and time, which can be easily retrieved via optical character recognition (OCR).

- **Quality assurance.** Monitor and analyze quality control in a manufacturing facility. By installing cameras on the production line, you can train a computer vision model to detect anomalies.

## Considerations

These considerations implement the pillars of the Azure Well-Architected Framework, a set of guiding tenets that you can use to improve the quality of a workload. For more information, see [Microsoft Azure Well-Architected Framework](/azure/well-architected/).

### Reliability

Reliability ensures your application can meet the commitments you make to your customers. For more information, see [Design review checklist for Reliability](/azure/well-architected/reliability/checklist).

A reliable workload is one that's both resilient and available. *Resiliency* is the ability of the system to recover from failures and continue to function. The goal of resiliency is to return the application to a fully functioning state after a failure occurs. *Availability* is a measure of whether your users can access your workload when they need to.

For the availability guarantees of the Azure services in this solution, see these resources:

- [Service-level agreement (SLA) for Storage Accounts](https://azure.microsoft.com/support/legal/sla/storage/v1_5)
- [SLA for Azure Machine Learning](https://azure.microsoft.com/support/legal/sla/machine-learning-service/v1_0)
- [SLA for Azure AI services](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1)
- [SLA for Logic Apps](https://azure.microsoft.com/support/legal/sla/logic-apps/v1_0)
- [SLA for Azure Synapse Analytics](https://azure.microsoft.com/support/legal/sla/synapse-analytics/v1_1)
- [SLA for Power BI](https://azure.microsoft.com/support/legal/sla/power-bi-embedded/v1_1)

### Security

Security provides assurances against deliberate attacks and the abuse of your valuable data and systems. For more information, see [Design review checklist for Security](/azure/well-architected/security/checklist).

Consider the following resources:

- [Identity management](/azure/architecture/framework/security/overview#identity-management)
- [Protect your infrastructure](/azure/architecture/framework/security/overview#protect-your-infrastructure)
- [Application security](/azure/architecture/framework/security/overview#application-security)
- [Data sovereignty and encryption](/azure/architecture/framework/security/overview#data-sovereignty-and-encryption)
- [Security resources](/azure/architecture/framework/security/overview#security-resources)

### Cost Optimization

Cost Optimization is about reducing unnecessary expenses and improving operational efficiencies. For more information, see [Design review checklist for Cost Optimization](/azure/well-architected/cost-optimization/checklist).

Here are some guidelines for optimizing costs:

- Use the pay-as-you-go strategy for your architecture, and [scale out](/azure/architecture/framework/cost/optimize-autoscale) as needed rather than investing in large-scale resources at the start.
- Consider opportunity costs in your architecture, and the balance between first-mover advantage versus fast follow. Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator) to estimate the initial cost and operational costs.
- Establish [policies](/azure/architecture/framework/cost/principles), [budgets, and controls](/azure/architecture/framework/cost/monitor-alert) that set cost limits for your solution.

### Operational Excellence

Operational Excellence covers the operations processes that deploy an application and keep it running in production. For more information, see [Design review checklist for Operational Excellence](/azure/well-architected/operational-excellence/checklist).

Deployments need to be reliable and predictable. Here are some guidelines:

- Automate deployments to reduce the chance of human error.
- Implement a fast, routine deployment process to avoid slowing down the release of new features and bug fixes.
- Quickly roll back or roll forward if an update causes problems.

### Performance Efficiency

Performance Efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. For more information, see [Design review checklist for Performance Efficiency](/azure/well-architected/performance-efficiency/checklist).

Appropriate use of scaling and the implementation of PaaS offerings that have built-in scaling are the main ways to achieve performance efficiency.

## Contributors

*This article is maintained by Microsoft. It was originally written by the following contributors.*

Principal author:

- [Oscar Shimabukuro Kiyan](https://www.linkedin.com/in/oscarshk) | Senior Cloud Solutions Architect – Data & AI

Other contributors:

- [Mick Alberts](https://www.linkedin.com/in/mick-alberts-a24a1414) | Technical Writer
- [Brandon Cowen](https://www.linkedin.com/in/brandon-cowen-1658211b) | Senior Cloud Solutions Architect – Data & AI
- [Arash Mosharraf](https://www.linkedin.com/in/arashaga) | Senior Cloud Solutions Architect – Data & AI
- [Priyanshi Singh](https://www.linkedin.com/in/priyanshi-singh5) | Senior Cloud Solutions Architect – Data & AI
- [Julian Soh](https://www.linkedin.com/in/juliansoh) | Director Specialist – Data & AI

*To see non-public LinkedIn profiles, sign in to LinkedIn.*

## Next steps

- [Introduction to Azure Storage](/azure/storage/common/storage-introduction)
- [What is Azure Machine Learning?](/azure/machine-learning/overview-what-is-azure-machine-learning)
- [What is Azure AI services?](/azure/cognitive-services/what-are-cognitive-services)
- [What is Azure Logic Apps?](/azure/logic-apps/logic-apps-overview)
- [What is Azure Synapse Analytics?](/azure/synapse-analytics/overview-what-is)
- [What is Power BI Embedded analytics?](/power-bi/developer/embedded/embedded-analytics-power-bi)
- See the [Business process automation solution](https://github.com/Azure/business-process-automation) on GitHub
