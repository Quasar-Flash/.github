# Integrando ao Sentry

Solicite um DSN para um dos administradores do Sentry. 
- Apps em FASTAPI devem ser criados como aplicação em ASGI. Não há uma opção nativa para o framework.

Como integrar o Sentry à sua aplicação:

### FastAPI

Crie um arquivo version.py no mesmo diretório de seu arquivo main:
```python
from io import open

read_version = lambda filename: open(filename, "r", encoding="utf-8").read()

""" It defines the project current version """
__version__ = read_version(".version").strip()

if __name__ == "__main__":
    print(__version__)

```

Adicione as importações no cabeçalho do arquivo main.py:
```python
from sentry_sdk.integrations.starlette import StarletteIntegration
from sentry_sdk.integrations.fastapi import FastApiIntegration

import sentry_sdk

from app.version import __version__

```

Inicialize o sdk informando o dns (SENTRY_URL) e um ambiente (production,development, homolog, etc.). 
Configure as variáveis de ambiente de modo que SENTRY_URL e SENTRY_ENVIRONMENT sejam vazios quando em ambiente local.

```python
sentry_sdk.init(
    SENTRY_URL,
    environment=SENTRY_ENVIRONMENT,
    send_default_pii=True,
    sample_rate=1.0,
    traces_sample_rate=1,
    release=__version__,
    integrations=[StarletteIntegration(), FastApiIntegration()],
)
```

Adicione o seguinte trecho ao final do arquivo main:
```python
@app.middleware("http")
async def sentry_exception(request: Request, call_next):
    try:
        response = await call_next(request)
        return response
    except Exception as ex:
        with sentry_sdk.push_scope() as scope:
            scope.set_context("request", request)
            scope.user = {"ip_address": request.client.host}
            sentry_sdk.capture_exception(ex)
        raise
```
