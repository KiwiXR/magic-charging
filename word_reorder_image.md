# 妈耶问道，怪力乱神

`reorder.py`
```python
import os


new_order = [
'ParallelSFLANovelSplitFederatedLearningFrameworkTacklingHeterogeneityIssues',
'BCleanABayesianDataCleaningSystem',
'EvolvingStandardizationforContinualDomainGeneralizationoverTemporalDrift',
'PrincipalPropertiesAttentionMatchingforPartialDomainAdaptationinFaultDiagnosis',
'Context-AwareAdaptiveWeightedAttentionNetworkforReal-TimeSurfaceDefectSegmentation',
'SePiCoSemantic-GuidedPixelContrastforDomainAdaptiveSemanticSegmentation',
'AcceleratingFederatedLearningWithDataandModelParallelisminEdgeComputing',
'BOSEBlock-WiseFederatedLearninginHeterogeneousEdgeComputing',
'Privacy-EnhancedDatabaseSynthesisforBenchmarkPublishing',
'ElasticDNNOn-deviceNeuralNetworkRemodelingforAdaptingEvolvingVisionDomainsatEdge',
'WeightDiffusionforFutureLearntoGeneralizeinNon-StationaryEnvironments',
'ReducingShape-RadianceAmbiguityinRadianceFieldswithaClosed-FormColorEstimationMethod',
'AcceleratingDecentralizedFederatedLearninginHeterogeneousEdgeComputing',
'AdaptiveControlofLocalUpdatingandModelCompressionforEfficientFederatedLearning',
'ComputationandCommunicationEfficientFederatedLearningWithAdaptiveModelPruning',
'FederatedLearningWithClientSelectionandGradientCompressioninHeterogeneousEdgeSystems',
'EnhancingFederatedLearningWithServer-SideUnlabeledDatabyAdaptiveClientandDataSelection',
'FINCHEnhancingFederatedLearningwithHierarchicalNeuralArchitectureSearch',
'DecentralizedFederatedLearningWithIntermediateResultsinMobileEdgeComputing',
'EnhancingDecentralizedandPersonalizedFederatedLearningwithTopologyConstruction',
'AdaptingAcrossDomainsviaTarget-orientedTransferableSemanticAugmentationunderPrototypeConstraint',
'MetaAuxiliaryLearningforTop-KRecommendation',
'JointModelPruningandTopologyConstructionforAcceleratingDecentralizedMachineLearning',
'MergeSFLSplitFederatedLearningwithFeatureMergingandBatchSizeRegulation',
'ClientsHelpClientsAlternatingCollaborationforSemi-SupervisedFederatedLearning',
'QEVISMulti-grainedVisualizationofDistributedQueryExecution',
'HiTailorInteractiveTransformationandVisualizationforHierarchicalTabularData',
'CoInsightVisualStorytellingforHierarchicalTableswithConnectedInsights',
'InsigHTableInsight-drivenHierarchicalTableVisualizationwithReinforcementLearning',
'HiRegExInteractiveVisualQueryandExplorationofMultivariateHierarchicalData',
'G2PxyGenerativeOpen-SetnodeClassificationonGraphswithProxyUnknowns',
'ReCoSANovelBenchmarkforCross-ModalImage-TextRetrievalinComplexReal-LifeScenarios',
'MakingTheBestofBothWorldsADomain-OrientedTransformerforUnsupervisedDomainAdaptation',
'BorrowingKnowledgeFromPre-trainedLanguageModelANewData-efficientVisualLearningParadigm',
'ImprovingGeneralizationwithDomainConvexGame',
'EvaluateGeometryofRadianceFieldswithLow-frequencyColorPrior',
'ImprovingVisionAnomalyDetectionwiththeGuidanceofLanguageModality',
'TokenVis：面向以太坊区块链ERC-20智能合约演变模式的可视分析方法',
'JANUSLatency-AwareTrafficSchedulingforIoTDataStreaminginEdgeEnvironments',
'FedVitFederatedContinualLearningofVisionTransformeratEdge',
'LearnedUnmannedVehicleSchedulingforLarge-scaleUrbanLogistics',
'JointAttention-GuidedFeatureFusionNetworkforSaliencyDetectionofSurfaceDefects',
'NonperiodicandPeriodicEvent-TriggeredOnlineH∞ControlforConstrainedNonlinearSystems',
'Foreground-BackgroundSeparationTransformerforWeaklySupervisedSurfaceDefectDetection',
'EnlargetheErrorPredictionDatasetin3DPrintingAnUnsupervisedDentalCrownMeshGenerator',
'Dirichlet-basedUncertaintyCalibrationforActiveDomainAdaptation',
'大数据管理前沿：理论、系统和应用',
'基于强化学习的自适应模型分层压缩联邦学习',
'面向边缘异构的端边云协同半监督联邦学习',
'基于互蒸馏技术的边云协同动态联邦学习',
'边缘侧神经网络块粒度领域自适应技术研究',
'基于纹理知识引导的跨域织物缺陷检测',
'平行车间调度模型构建和系统设计',
'融合先验知识推理的表面缺陷检测',
'纺织品表面缺陷知识图谱构建研究',
'基于量子框架的喷气织机控制系统设计与开发',
'面向离散制造业的工业互联网数据湖平台构建与应用',
'基于数据湖的工业数据管理体系构建研究',
'DCTRANS基于DDQN的否定约束规则迁移',
'面向离散行业的虚实双场景工业引擎研究',
'AdaptiveConfigurationforHeterogeneousParticipantsinDecentralizedFederatedLearning',
'Heterogeneity-AwareFederatedLearningwithAdaptiveClientSelectionandGradientCompression',
'Experience-drivenNetworkTopologyConstructionforEfficientDecentralizedFederatedLearning',
'TabMentorDetectErrorsonTabularDatawithNoisyLabels',
'AdaptiveLabelCleaningforErrorDetectiononTabularData',
'NeuronDPNeuron-grainedDifferentialPrivacyofDeepNeuralNetworksinEdge-basedFederatedLearning',
'MemoryRelevantHyperparametersOptimizationforDNNTrainingatEdge',
'WeightedMultipleSource-FreeDomainAdaptationEnsembleNetworkinIntelligentMachineryFaultDiagnosis',
'Multi-AgentReinforcementLearningforExtendedFlexibleJobShopScheduling',
'MILPModelsforFlexibleJobShopSchedulingwithSpatialConstraintsandSequenceFlexibility',
'AParallelLearningApproachfortheFlexibleJobShopSchedulingProblem',
'HierarchicalSymmetricCrossEntropyforDistantSupervisedRelationExtraction',
]

dest_dir = 'reordered_images'
if not os.path.exists(dest_dir):
    os.makedirs(dest_dir)
idx = 0
for cur in new_order:
    for file in sorted(os.listdir(cur)):
        if file.startswith('image'):
            file_name, ext = os.path.splitext(file)
            idx += 1
            new_name = f'image{idx}{ext}'
            # copy to new folder
            src_path = os.path.join(cur, file)
            dest_path = os.path.join(dest_dir, new_name)
            with open(src_path, 'rb') as src_file:
                with open(dest_path, 'wb') as dest_file:
                    dest_file.write(src_file.read())
            print(f'Copying {src_path} to {dest_path}')
```
`solve.py`
```python
import re
from docx import Document
from docx.enum.text import WD_PARAGRAPH_ALIGNMENT  # 导入段落对齐枚举
from docx.shared import Inches
import os
from PIL import Image
from PIL import UnidentifiedImageError

# 创建或加载 Word 文档
docx_name = '3.4 论文发表情况.docx'

doc = Document()

doc1 = Document(f'../{docx_name}')

# 图片目录
images_dir = "reordered_images"

# 获取图片列表并按名称中的数字大小排序
def extract_number(filename):
    match = re.search(r'image(\d+)', filename)  # 提取文件名中的数字
    return int(match.group(1)) if match else float('inf')  # 如果没有数字，放在最后

image_files = sorted(
    [f for f in os.listdir(images_dir) if f.endswith(('.png', '.jpg', '.jpeg', '.emf'))],
    key=extract_number
)

# 获取页面宽度（以英寸为单位）
section = doc1.sections[0]
page_width = section.page_width.inches - section.left_margin.inches - section.right_margin.inches
page_height = section.page_height.inches - section.top_margin.inches - section.bottom_margin.inches

def convert_emf_to_png(emf_path):
    try:
        with Image.open(emf_path) as img:
            png_path = emf_path.replace('.emf', '_converted.png')
            img.save(png_path, 'PNG')
            return png_path
    except Exception as e:
        print(f"无法转换 .emf 文件: {emf_path}, 错误: {e}")
        return None

# 按顺序插入图片
for image_file in image_files:
    image_path = os.path.join(images_dir, image_file)
    if os.path.exists(image_path):
        try:
            # 检查图片是否有效
            with Image.open(image_path) as img:
                img.verify()

            # 检查是否为 emf 格式
            if image_file.lower().endswith('.emf'):
                converted_path = convert_emf_to_png(image_path)
                if converted_path is None:
                    print(f"跳过无法处理的 .emf 文件: {image_path}")
                    continue
                else:
                    image_path = converted_path
            # 处理其他格式的图片（保持原逻辑）
            with Image.open(image_path) as img:
                if img.width > img.height:
                    img = img.rotate(-90, expand=True)
                    temp_path = os.path.join(images_dir, f"rotated_{image_file}")
                    img.save(temp_path)
                    image_path = temp_path
                aspect_ratio = img.width / img.height
                adjusted_width = page_width * 72
                adjusted_height = adjusted_width / aspect_ratio
                if adjusted_height > page_height * 72:
                    adjusted_height = page_height * 72
                    adjusted_width = adjusted_height * aspect_ratio
                doc.add_picture(image_path, width=Inches(adjusted_width / 72), height=Inches(adjusted_height / 72))
                last_paragraph = doc.paragraphs[-1]
                last_paragraph.alignment = WD_PARAGRAPH_ALIGNMENT.CENTER
        except (UnidentifiedImageError, FileNotFoundError):
            print(f"无法识别或找到图片: {image_path}")
        except Exception as e:
            print(f"插入图片失败: {image_path}, 错误: {e}")
    else:
        print(f"图片不存在: {image_path}")

# 保存新的 Word 文档
name, ext = os.path.splitext(docx_name)
new_docx_name = f'{name}(1).docx'
doc.save(new_docx_name)
print(f"图片已按顺序插入，保存为: {new_docx_name}")
```
