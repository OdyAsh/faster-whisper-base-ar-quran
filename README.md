---
license: apache-2.0
language:
- ar
base_model:
- tarteel-ai/whisper-base-ar-quran
- openai/whisper-base
pipeline_tag: automatic-speech-recognition
tags:
- generated_from_trainer
- islam
- quran
- arabic
- asr
- audio
metrics:
- wer
model-index:
- name: faster-whisper-base-ar-quran
  results: []
---

# faster-whisper-base-ar-quran

[This model](https://huggingface.co/OdyAsh/faster-whisper-base-ar-quran) is a CTranslate2 version of [tarteel-ai/whisper-base-ar-quran](https://huggingface.co/tarteel-ai/whisper-base-ar-quran). The conversion was performed using the following command:

```bash
# Don't add vocab.json and config.json , as they already get created while converting to new model
ct2-transformers-converter --model tarteel-ai/whisper-base-ar-quran --force --output_dir "path/to/output/dir/faster-whisper-base-ar-quran"  --quantization float16 --copy_files added_tokens.json normalizer.json preprocessor_config.json special_tokens_map.json tokenizer_config.json
```

To use the `ct2-transformers-converter` command, you'll need to install the required dependencies:
```bash
pip install transformers[torch]
```
For more information about the converter, see the [CTranslate2 documentation](https://opennmt.net/CTranslate2/guides/transformers.html) or the "CTranslate2 Installation" section below

This conversion transforms the model from OpenAI's [vanilla Whisper family](https://huggingface.co/openai?search_models=whisper) to the [faster-whisper family](https://huggingface.co/Systran), making it compatible with [WhisperX](https://github.com/m-bain/whisperX) which utilizes faster-whisper versions for improved performance. 
* For reference: "WhisperX enables significantly faster transcription speeds - up to 70x realtime with large-v2 models while requiring less than 8GB GPU memory."

## Usage Example

Check out the surah-splitter repo [here](https://github.com/OdyAsh/surah-splitter/blob/8c2889aecb1eb00a856225d680ca08ec8fded8af/src/surah_splitter/app/main_cli.py#L236) and [here](https://github.com/OdyAsh/surah-splitter/blob/8c2889aecb1eb00a856225d680ca08ec8fded8af/src/surah_splitter/quran_toolkit/surah_processor.py#L70).

## Quantization Side Note

You'll see that in the command above, we're using `--quantization float16`. However, the original `tarteel-ai/whisper-base-ar-quran` is in float32 precision ([source](https://huggingface.co/tarteel-ai/whisper-base-ar-quran/blob/main/config.json#L37)). Yet, float16 conversion was kept for the following reasons:
* Reduced size: 141mb instead of [290mb](https://huggingface.co/tarteel-ai/whisper-base-ar-quran/blob/main/pytorch_model.bin).
* Negligible performance impact for the usecase in mind: When this model was tested on sample Quran audio files, there were few transcription errors that didn't affect the overall performance of [OdyAsh's whisperx solution](https://github.com/OdyAsh/surah-splitter) which used this model, since the subsequent steps in that solution's pipeline (e.g., alignment, reference <-> input matching DP algorithm, etc.) still yielded accurate results.

However, if the transcription results are not satisfactory for your use case, you can always convert the model to float32 precision by:
* Changing the `--quantization` argument to `float32` in the command above to get a larger model size (around 290mb).
* Or, during inference runtime, you can set the `compute_type` argument of [whisperx.load_model()](https://github.com/m-bain/whisperX/tree/main?tab=readme-ov-file#python-usage-) to `float32`.
  * Read [this](https://github.com/OpenNMT/CTranslate2/blob/master/docs/quantization.md#:~:text=bfloat16-,For%20example,-%2C) and [this](https://www.perplexity.ai/search/search-about-the-ct2-transform-GdKee50NTp.iIOnoHIEuBw) for proof that the model can be recomputed to float32 precision at runtime.

## CTranslate2 Installation

In the [OdyAsh/faster-whisper-base-ar-quran](https://github.com/OdyAsh/faster-whisper-base-ar-quran) GitHub repo, you'll see `pyproject.toml` and `uv.lock` files, indicating that you can use `uv` to install the required packages for the `ct2-transformers-converter` command instead of pip (if you want).

Steps:

1. Install `uv` if not already installed by following [this](https://docs.astral.sh/uv/getting-started/installation/#standalone-installer) section in their docs.

2. In your terminal, navigate to your local directory in which you cloned the [OdyAsh/faster-whisper-base-ar-quran](https://github.com/OdyAsh/faster-whisper-base-ar-quran) GitHub repo.

3. Install the required packages right away (since `uv.lock` file is already present in that local directory):

```bash
uv install
```

4. Verify installation:

```bash
ct2-transformers-converter --help
```

