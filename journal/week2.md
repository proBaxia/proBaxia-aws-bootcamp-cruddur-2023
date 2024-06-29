# Week 2 — Distributed Tracing

#### Add the following ENV Vars to backend.flask in docker compose

~~~
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api_honeycomb.in"

OTEL_EXPORTER_OTLP_HEADERS:"*-honeycomb-$(HONEYCOMB_API_KEY)"

OTEL_SERVICE_NAME: "$(HONEYCOMB_SERVICE_NAME)"
~~~


#### create a  `honey comb.io` account and create an environment and generate the `API KEY`

- Honeycomb's auto-instrumentation allows you to send basic data quickly using the OpenTelemetry industry standard, before adding custom context.

- copy `API` to your environment variables 
~~~ 
- export HONEYCOMB_API_KEYS="123456789qwertyuio"
- gp env HONEYCOMB_API_KEYS="123456789qwertyuio"
- export HONEYCOMB_SERVICE_NAME="Cruddur"
- gp env HONEYCOMB_SERVICE_NAME="Cruddur"
~~~
 check if your environment variables `env | grep HONEY`

## HoneyComb
When Creating a new dataset in Honeycomb it will provide all these installation instructions

we'll  add the follwing files to our `requirements.txt`

~~~
opentelemetry-api
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http
opentelemetry-instrumentation-flask
opentelemetry-instrumentation-requests
~~~

on your terminal install requirements.txt
~~~
pip install -r  requirements.txt
~~~


#### Update your `app.py`file with this code 
~~~py

# HoneyComb 
from opentelemetry import trace
from opentelemetry.opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.opentelemetry.exporter.otlp.proto.http.trace_exporter import OTPLSpanExporter
from opentelemetry.opentelemetry.sdk.trace import TraceProvider
from opentelemetry.opentelemetry.sdk.export import BatchSparProcessor

# Honeycomb
# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
app = Flask(__name__)

# Honeycomb
# Initialize automatic instrumentation with Flask
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()

~~~ 
