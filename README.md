# AudioSpoof 虚假音频检测数据集

随着TTS（Text-to-Speech）技术的快速发展，当前语音克隆模型生成的声音已难以通过简单听觉判断真伪。然而，针对**中文场景的音频伪造检测**领域仍存在显著空白：  
1️⃣ 缺乏基于最新语音合成技术生成的伪造音频数据集（Audio Spoofing Dataset）  
2️⃣ 现有检测方法对零样本语音克隆攻击的防御能力不足  

为此，我们基于 [MagicData 中文普通话语料库](https://www.magicdatatech.cn/)，通过四大前沿开源TTS模型进行零样本语音克隆: [NaturalSpeech3](https://github.com/open-mmlab/Amphion/blob/main/models/codec/ns3_codec/README.md), [CosyVoice](https://github.com/FunAudioLLM/CosyVoice)  , [F5-TTS](https://github.com/SWivid/F5-TTS), [Spark-TTS](https://github.com/SparkAudio/Spark-TTS), 构建首个专注于中文场景的多模型伪造音频检测基准数据集。采用零样本克隆，可以获取较高质量的、较多人数的伪造音频数据集。  


## 数据集下载

数据集已托管至 Hugging Face:[AudioSpoof](https://huggingface.co/datasets/HuShou-ZMZN/audiofake) 和 [zenodo](https://zenodo.org/records/15259855)


## 数据构建方法
### 基础数据源
从原始数据集中分层抽样构建核心语料：
- **开发集**：随机抽取 10% 说话人（2人），每人随机选取 10% 录音
- **测试集**：随机抽取 10% 说话人（4人），同比例采样
- **训练集**：抽取 2% 说话人（20人），保持相同采样率

### 语音克隆流程
采用零样本克隆技术生成样本：
```
graph LR
    A[原始音频] --> B(TTS模型)
    B --> C{克隆音频}
```

## 数据结构

```
AudioSpoof/
├── metadata/
│   └── SPKINFO.txt                  # 录音人元数据
├── wav/
│   ├── dev/                         # 原始开发集音频
│   │   ├── {speaker_id}/            # 如 5_541/
│   │   │   └── {utterance_id}.wav   # 如 5_541_20170607142131.wav
│   ├── test/                        # 原始测试集音频（结构同 dev）
│   ├── train/                       # 原始训练集音频（结构同 dev）
│   │
│   ├── dev-naturalspeech3/          # 语音克隆结果（模型1）
│   ├── test-naturalspeech3/         # （结构与原始 wav 目录保持一致）
│   ├── train-naturalspeech3/
│   │
│   ├── dev-cosyvoice/            # 语音克隆结果（模型2）
│   ├── test-cosyvoice/
│   ├── train-cosyvoice/
│   │
│   ├── dev-F5TTS/                  # 语音克隆结果（模型3）
│   ├── test-F5TTS/
│   ├── train-F5TTS/
│   │
│   └── dev-sparktts/               # 语音克隆结果（模型4）
│   ├── test-sparktts/
│   └── train-sparktts/
└── text/
    ├── dev.txt                      # 开发集文本（格式：UtteranceID SpeakerID Transcription）
    ├── test.txt                     # 测试集文本
    └── train.txt                    # 训练集文本

```
关键统计： 
| 子集          | 说话人数   | 真实音频数 | 伪造音频数（×4模型） | 总样本数 |
| ------------- | ---------- | ---------- | -------------------- | -------- |
| dev           | 2          | 118        | 472                  | 590      |
| test          | 4          | 180        | 720                  | 900      |
| train         | 20         | 1105       | 4,420                | 5,525    |

 总计：26 人 | 1,403 真实 | 5,612 伪造 | 7,015 总样本  

### 关键说明： 
1. 每个克隆模型生成的三组目录（dev/test/train）保持原始音频目录结构
2. 语音克隆目录命名统一采用 `{子集}-{model_name}` 格式（如 dev-naturalspeech3）
3. 克隆音频文件保留原始命名（如 5_541_20170607142131.wav），仅通过目录路径区分不同模型的输出



