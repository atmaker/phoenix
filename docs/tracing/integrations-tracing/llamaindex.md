---
description: How to use the python LlamaIndexInstrumentor to trace LlamaIndex
---

# LlamaIndex

{% embed url="https://colab.research.google.com/github/Arize-ai/phoenix/blob/main/tutorials/tracing/llama_index_tracing_tutorial.ipynb" %}
Troubleshooting an LLM application using the OpenInferenceTraceCallback
{% endembed %}

[LlamaIndex](https://github.com/run-llama/llama\_index) is a data framework for your LLM application. It's a powerful framework by which you can build an application that leverages RAG (retrieval-augmented generation) to super-charge an LLM with your own data. RAG is an extremely powerful LLM application model because it lets you harness the power of LLMs such as OpenAI's GPT but tuned to your data and use-case.

For LlamaIndex, tracing instrumentation is added via an OpenTelemetry instrumentor aptly named the `LlamaIndexInstrumentor` . This callback is what is used to create spans and send them to the Phoenix collector.

{% tabs %}
{% tab title="Instrumentation (>=0.10.43)" %}
Phoenix supports LlamaIndex's latest [instrumentation](https://docs.llamaindex.ai/en/stable/module\_guides/observability/instrumentation/) paradigm.

To get started, make sure you're running Phoenix. The easiest way to do this is to run the following in a separate terminal window:

```
pip install arize-phoenix
phoenix serve
```

See [deployment](../../deployment/ "mention")for other hosting options.

Then, pip install the following in your project.

{% code overflow="wrap" %}
```
pip install "llama-index-core>=0.10.43" "openinference-instrumentation-llama-index>=2" "opentelemetry-proto>=1.12.0" arize-phoenix-otel
```
{% endcode %}

Use the following code snippet to activate the instrumentation.

Note that the `endpoint` variable below should the address of the Phoenix receiver.

```python
from openinference.instrumentation.llama_index import LlamaIndexInstrumentor
from phoenix.otel import register

tracer_provider = register(
  project_name="my-llm-app", # Default is 'default'
  endpoint="http://localhost:6006/v1/traces",
)

LlamaIndexInstrumentor().instrument(tracer_provider=tracer_provider)
```
{% endtab %}

{% tab title="Legacy One-Click (<0.10.43)" %}
{% hint style="info" %}
Using phoenix as a callback requires an install of \`llama-index-callbacks-arize-phoenix>0.1.3'
{% endhint %}

llama-index 0.10 introduced modular sub-packages. To use llama-index's one click,  you must install the small integration first:

```bash
pip install 'llama-index-callbacks-arize-phoenix>0.1.3'
```

```python
# Phoenix can display in real time the traces automatically
# collected from your LlamaIndex application.
import phoenix as px
# Look for a URL in the output to open the App in a browser.
px.launch_app()
# The App is initially empty, but as you proceed with the steps below,
# traces will appear automatically as your LlamaIndex application runs.

from llama_index.core import set_global_handler

set_global_handler("arize_phoenix")

# Run all of your LlamaIndex applications as usual and traces
# will be collected and displayed in Phoenix.
```
{% endtab %}

{% tab title="Legacy (<v0.10)" %}
If you are using an older version of llamaIndex (pre-0.10), you can still use phoenix. You will have to be using `arize-phoenix>3.0.0` and downgrade `openinference-instrumentation-llama-index<1.0.0`

```python
# Phoenix can display in real time the traces automatically
# collected from your LlamaIndex application.
import phoenix as px
# Look for a URL in the output to open the App in a browser.
px.launch_app()
# The App is initially empty, but as you proceed with the steps below,
# traces will appear automatically as your LlamaIndex application runs.

import llama_index
llama_index.set_global_handler("arize_phoenix")

# Run all of your LlamaIndex applications as usual and traces
# will be collected and displayed in Phoenix.
```
{% endtab %}
{% endtabs %}

By adding the callback to the callback manager of LlamaIndex, we've created a one-way data connection between your LLM application and Phoenix Server.

For a fully working example of tracing with LlamaIndex, checkout our colab notebook.

