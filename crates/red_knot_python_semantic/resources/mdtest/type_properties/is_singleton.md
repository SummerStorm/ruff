# Singleton types

A type is a singleton type iff it has exactly one inhabitant.

## Basic

```py
from typing_extensions import Literal, Never, Callable
from knot_extensions import is_singleton, static_assert

static_assert(is_singleton(None))
static_assert(is_singleton(Literal[True]))
static_assert(is_singleton(Literal[False]))

static_assert(is_singleton(type[bool]))

static_assert(not is_singleton(Never))
static_assert(not is_singleton(str))

static_assert(not is_singleton(Literal[345]))
static_assert(not is_singleton(Literal[1, 2]))

static_assert(not is_singleton(tuple[()]))
static_assert(not is_singleton(tuple[None]))
static_assert(not is_singleton(tuple[None, Literal[True]]))

static_assert(not is_singleton(Callable[..., None]))
static_assert(not is_singleton(Callable[[int, str], None]))
```

## `NoDefault`

### 3.12

```toml
[environment]
python-version = "3.12"
```

```py
from typing_extensions import _NoDefaultType
from knot_extensions import is_singleton, static_assert

static_assert(is_singleton(_NoDefaultType))
```

### 3.13

```toml
[environment]
python-version = "3.13"
```

```py
from typing import _NoDefaultType
from knot_extensions import is_singleton, static_assert

static_assert(is_singleton(_NoDefaultType))
```

## `builtins.ellipsis`/`types.EllipsisType`

### All Python versions

The type of the builtin symbol `Ellipsis` is the same as the type of an ellipsis literal (`...`).
The type is not actually exposed from the standard library on Python \<3.10, but we still recognise
the type as a singleton on any Python version.

```toml
[environment]
python-version = "3.9"
```

```py
from knot_extensions import is_singleton, static_assert

static_assert(is_singleton(Ellipsis.__class__))
static_assert(is_singleton((...).__class__))
```

### Python 3.10+

On Python 3.10+, the standard library exposes the type of `...` as `types.EllipsisType`, and we also
recognise this as a singleton type when it is referenced directly:

```toml
[environment]
python-version = "3.10"
```

```py
import types
from knot_extensions import static_assert, is_singleton

static_assert(is_singleton(types.EllipsisType))
```

## `builtins.NotImplemented` / `types.NotImplementedType`

### All Python versions

Just like `Ellipsis`, the type of `NotImplemented` was not exposed on Python \<3.10. However, we
still recognize the type as a singleton in all Python versions.

```toml
[environment]
python-version = "3.9"
```

```py
from knot_extensions import is_singleton, static_assert

static_assert(is_singleton(NotImplemented.__class__))
```

### Python 3.10+

On Python 3.10+, the standard library exposes the type of `NotImplemented` as
`types.NotImplementedType`. We also recognize this as a singleton type when it is referenced
directly:

```toml
[environment]
python-version = "3.10"
```

```py
import types
from knot_extensions import static_assert, is_singleton

# TODO: types.NotImplementedType is a TypeAlias of builtins._NotImplementedType
# Once TypeAlias support is added, it should satisfy `is_singleton`
reveal_type(types.NotImplementedType)  # revealed: Unknown | Literal[_NotImplementedType]
static_assert(not is_singleton(types.NotImplementedType))
```
