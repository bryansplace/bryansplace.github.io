---
title: DS4SD/docling: 🥚 Transform PDF to JSON or Markdown with ease and speed 🐣
source: https://github.com/DS4SD/docling/tree/main
---

[![Docling](https://github.com/DS4SD/docling/raw/main/logo.png)](https://github.com/ds4sd/docling)

[![arXiv](https://camo.githubusercontent.com/2610bebade789d244a2ef0446592fc58fe1c4506570f064e65e1b2b77b1879cf/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f61725869762d323430382e30393836392d6233316231622e737667)](https://arxiv.org/abs/2408.09869) [![PyPI version](https://camo.githubusercontent.com/0798c9df01764984318db919ff970737823a3cab2fd3608b4cfde31afb5f18d2/68747470733a2f2f696d672e736869656c64732e696f2f707970692f762f646f636c696e67)](https://pypi.org/project/docling/) [![Python](https://camo.githubusercontent.com/2439533e7c0f101ce3666c3b41f34ec30f9fba28d0a99b786205422896ad0573/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f707974686f6e2d332e3130253230253743253230332e3131253230253743253230332e31322d626c7565)](https://camo.githubusercontent.com/2439533e7c0f101ce3666c3b41f34ec30f9fba28d0a99b786205422896ad0573/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f707974686f6e2d332e3130253230253743253230332e3131253230253743253230332e31322d626c7565) [![Poetry](https://camo.githubusercontent.com/e9de59b7d2a7896f05d977ca76c28c69c6ff163840e5526baeb18e56c532ad5f/68747470733a2f2f696d672e736869656c64732e696f2f656e64706f696e743f75726c3d68747470733a2f2f707974686f6e2d706f657472792e6f72672f62616467652f76302e6a736f6e)](https://python-poetry.org/) [![Code style: black](https://camo.githubusercontent.com/5bf9e9fa18966df7cb5fac7715bef6b72df15e01a6efa9d616c83f9fcb527fe2/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f636f64652532307374796c652d626c61636b2d3030303030302e737667)](https://github.com/psf/black) [![Imports: isort](https://camo.githubusercontent.com/4e71e9b7ea25fbc70f186444684f4bfd9def4c737dfc327796cc2c332cbf0b46/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f253230696d706f7274732d69736f72742d2532333136373462313f7374796c653d666c6174266c6162656c436f6c6f723d656638333336)](https://pycqa.github.io/isort/) [![Pydantic v2](https://camo.githubusercontent.com/1ec3b5f774c66556456b4b855a73c1706f5454fa0ac3d2e4bcdabda9153b6b45/68747470733a2f2f696d672e736869656c64732e696f2f656e64706f696e743f75726c3d68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f707964616e7469632f707964616e7469632f6d61696e2f646f63732f62616467652f76322e6a736f6e)](https://pydantic.dev) [![pre-commit](https://camo.githubusercontent.com/c8dfc1d0c35fe0cc438cf57a44b9d915e0baa2aef9da75d07ad3ee2cdb237214/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f7072652d2d636f6d6d69742d656e61626c65642d627269676874677265656e3f6c6f676f3d7072652d636f6d6d6974266c6f676f436f6c6f723d7768697465)](https://github.com/pre-commit/pre-commit) [![License MIT](https://camo.githubusercontent.com/da848c100107c1ea42b6af14f02d9eb2d9e93d52976cdd71ca1767d17fcc9120/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f44533453442f646f636c696e67)](https://opensource.org/licenses/MIT)

Docling bundles PDF document conversion to JSON and Markdown in an easy, self-contained package.

* ⚡ Converts any PDF document to JSON or Markdown format, stable and lightning fast
* 📑 Understands detailed page layout, reading order and recovers table structures
* 📝 Extracts metadata from the document, such as title, authors, references and language
* 🔍 Optionally applies OCR (use with scanned PDFs)
* 🤖 Integrates easily with LLM app / RAG frameworks like 🦙 LlamaIndex and 🦜🔗 LangChain

To use Docling, simply install `docling` from your package manager, e.g. pip:  

Note

Works on macOS and Linux environments. Windows platforms are currently not tested.  

### Use alternative PyTorch distributions

[](#use-alternative-pytorch-distributions)

The Docling models depend on the [PyTorch](https://pytorch.org/) library. Depending on your architecture, you might want to use a different distribution of `torch`. For example, you might want support for different accelerator or for a cpu-only version. All the different ways for installing `torch` are listed on their website <https://pytorch.org/>.

One common situation is the installation on Linux systems with cpu-only support. In this case, we suggest the installation of Docling with the following options  

```
# Example for installing on the Linux cpu-only version
pip install docling --extra-index-url https://download.pytorch.org/whl/cpu
```

To develop for Docling, you need Python 3.10 / 3.11 / 3.12 and Poetry. You can then install from your local clone's root dir:  

```
poetry install --all-extras
```

### Convert a single document

[](#convert-a-single-document)

To convert invidual PDF documents, use `convert_single()`, for example:  

```
from docling.document_converter import DocumentConverter

source = "https://arxiv.org/pdf/2408.09869"  # PDF path or URL
converter = DocumentConverter()
result = converter.convert_single(source)
print(result.render_as_markdown())  # output: "## Docling Technical Report[...]"
```

### Convert a batch of documents

[](#convert-a-batch-of-documents)

For an example of batch-converting documents, see [batch_convert.py](https://github.com/DS4SD/docling/blob/main/examples/batch_convert.py).

From a local repo clone, you can run it with:  

    python examples/batch_convert.py

The output of the above command will be written to `./scratch`.

The example file [custom_convert.py](https://github.com/DS4SD/docling/blob/main/examples/custom_convert.py) contains multiple ways one can adjust the conversion pipeline and features.

You can control if table structure recognition or OCR should be performed by arguments passed to `DocumentConverter`:  

```
doc_converter = DocumentConverter(
    artifacts_path=artifacts_path,
    pipeline_options=PipelineOptions(
        do_table_structure=False,  # controls if table structure is recovered
        do_ocr=True,  # controls if OCR is applied (ignores programmatic content)
    ),
)
```

#### Control table extraction options

[](#control-table-extraction-options)

You can control if table structure recognition should map the recognized structure back to PDF cells (default) or use text cells from the structure prediction itself. This can improve output quality if you find that multiple columns in extracted tables are erroneously merged into one.  

```
pipeline_options = PipelineOptions(do_table_structure=True)
pipeline_options.table_structure_options.do_cell_matching = False  # uses text cells predicted from table structure model

doc_converter = DocumentConverter(
    artifacts_path=artifacts_path,
    pipeline_options=pipeline_options,
)
```

### Impose limits on the document size

[](#impose-limits-on-the-document-size)

You can limit the file size and number of pages which should be allowed to process per document:  

```
conv_input = DocumentConversionInput.from_paths(
    paths=[Path("./test/data/2206.01062.pdf")],
    limits=DocumentLimits(max_num_pages=100, max_file_size=20971520)
)
```

### Convert from binary PDF streams

[](#convert-from-binary-pdf-streams)

You can convert PDFs from a binary stream instead of from the filesystem as follows:  

```
buf = BytesIO(your_binary_stream)
docs = [DocumentStream(filename="my_doc.pdf", stream=buf)]
conv_input = DocumentConversionInput.from_streams(docs)
results = doc_converter.convert(conv_input)
```

You can limit the CPU threads used by Docling by setting the environment variable `OMP_NUM_THREADS` accordingly. The default setting is using 4 CPU threads.

Check out the following examples showcasing RAG using Docling with standard LLM application frameworks:

* [Basic RAG pipeline with 🦙 LlamaIndex](https://github.com/DS4SD/docling/tree/main/examples/rag_llamaindex.ipynb)
* [Basic RAG pipeline with 🦜🔗 LangChain](https://github.com/DS4SD/docling/tree/main/examples/rag_langchain.ipynb)

For more details on Docling's inner workings, check out the [Docling Technical Report](https://arxiv.org/abs/2408.09869).

Please read [Contributing to Docling](https://github.com/DS4SD/docling/blob/main/CONTRIBUTING.md) for details.

If you use Docling in your projects, please consider citing the following:  

```
@techreport{Docling,
  author = {Deep Search Team},
  month = {8},
  title = {Docling Technical Report},
  url = {https://arxiv.org/abs/2408.09869},
  eprint = {2408.09869},
  doi = {10.48550/arXiv.2408.09869},
  version = {1.0.0},
  year = {2024}
}
```

The Docling codebase is under MIT license. For individual model usage, please refer to the model licenses found in the original packages.
