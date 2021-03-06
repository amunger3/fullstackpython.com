title: sqlalchemy.dialects sqlite Example Code
category: page
slug: sqlalchemy-dialects-sqlite-examples
sortorder: 500031004
toc: False
sidebartitle: sqlalchemy.dialects sqlite
meta: Python example code that shows how to use the sqlite callable from the sqlalchemy.dialects module of the SQLAlchemy project.


sqlite is a callable within the sqlalchemy.dialects module of the SQLAlchemy project.


## Example 1 from sqlalchemy-datatables
[sqlalchemy-datatables](https://github.com/Pegase745/sqlalchemy-datatables)
([PyPI package information](https://pypi.org/project/sqlalchemy-datatables/))
is a helper library that makes it easier to use [SQLAlchemy](/sqlalchemy.html)
with the jQuery [JavaScript](/javascript.html)
[DataTables](https://datatables.net/) plugin. This library is designed to
be [web framework](/web-frameworks.html) agnostic and provides code examples
for both [Flask](/flask.html) and [Pyramid](/pyramid.html).

The project is built and maintained by
[Michel Nemnom (Pegase745)](https://github.com/Pegase745) and is open
sourced under the
[MIT license](https://github.com/Pegase745/sqlalchemy-datatables/blob/master/LICENSE).

[**sqlalchemy-datatables / datatables / datatables.py**](https://github.com/Pegase745/sqlalchemy-datatables/blob/master/./datatables/datatables.py)

```python
# datatables.py
from __future__ import absolute_import

import math

from sqlalchemy import Text, func, or_
~~from sqlalchemy.dialects import mysql, postgresql, sqlite

from datatables.clean_regex import clean_regex
from datatables.search_methods import SEARCH_METHODS


class DataTables:

    def __init__(self, request, query, columns, allow_regex_searches=False):
        self.params = dict(request)
        if 'sEcho' in self.params:
            raise ValueError(
                'Legacy datatables not supported, upgrade to >=1.10')
        self.query = query
        self.columns = columns
        self.results = None
        self.allow_regex_searches = allow_regex_searches

        self.cardinality_filtered = 0

        self.cardinality = 0

        self.yadcf_params = []
        self.filter_expressions = []
        self.error = None


## ... source file abbreviated to get to sqlite examples ...


            if direction == 'asc':
                sort_expr = sort_expr.asc()
            elif direction == 'desc':
                sort_expr = sort_expr.desc()
            else:
                raise ValueError(
                    'Invalid order direction: {}'.format(direction))
            if column.nulls_order:
                if column.nulls_order == 'nullsfirst':
                    sort_expr = sort_expr.nullsfirst()
                elif column.nulls_order == 'nullslast':
                    sort_expr = sort_expr.nullslast()
                else:
                    raise ValueError(
                        'Invalid order direction: {}'.format(direction))

            sort_expressions.append(sort_expr)
            i += 1
        self.sort_expressions = sort_expressions

    def _get_regex_operator(self):
        if isinstance(self.query.session.bind.dialect, postgresql.dialect):
            return '-'
        elif isinstance(self.query.session.bind.dialect, mysql.dialect):
            return 'REGEXP'
~~        elif isinstance(self.query.session.bind.dialect, sqlite.dialect):
            return 'REGEXP'
        else:
            raise NotImplementedError(
                'Regex searches are not implemented for this dialect')



## ... source file continues with no further sqlite examples...

```


## Example 2 from sqlalchemy-utils
[sqlalchemy-utils](https://github.com/kvesteri/sqlalchemy-utils)
([project documentation](https://sqlalchemy-utils.readthedocs.io/en/latest/)
and
[PyPI package information](https://pypi.org/project/SQLAlchemy-Utils/))
is a code library with various helper functions and new data types
that make it easier to use [SQLAlchemy](/sqlalchemy.html) when building
projects that involve more specific storage requirements such as
[currency](https://sqlalchemy-utils.readthedocs.io/en/latest/data_types.html#module-sqlalchemy_utils.types.currency).
The wide array of
[data types](https://sqlalchemy-utils.readthedocs.io/en/latest/data_types.html)
includes [ranged values](https://sqlalchemy-utils.readthedocs.io/en/latest/range_data_types.html)
and [aggregated attributes](https://sqlalchemy-utils.readthedocs.io/en/latest/aggregates.html).

[**sqlalchemy-utils / sqlalchemy_utils / types / password.py**](https://github.com/kvesteri/sqlalchemy-utils/blob/master/sqlalchemy_utils/types/password.py)

```python
# password.py
import weakref

import six
from sqlalchemy import types
~~from sqlalchemy.dialects import oracle, postgresql, sqlite
from sqlalchemy.ext.mutable import Mutable

from ..exceptions import ImproperlyConfigured
from .scalar_coercible import ScalarCoercible

passlib = None
try:
    import passlib
    from passlib.context import LazyCryptContext
except ImportError:
    pass


class Password(Mutable, object):

    @classmethod
    def coerce(cls, key, value):
        if isinstance(value, Password):
            return value

        if isinstance(value, (six.string_types, six.binary_type)):
            return cls(value, secret=True)

        super(Password, cls).coerce(key, value)


## ... source file abbreviated to get to sqlite examples ...



        return self._max_length

    def calculate_max_length(self):
        max_lengths = [1024]
        for name in self.context.schemes():
            scheme = getattr(__import__('passlib.hash').hash, name)
            length = 4 + len(scheme.name)
            length += len(str(getattr(scheme, 'max_rounds', '')))
            length += (getattr(scheme, 'max_salt_size', 0) or 0)
            length += getattr(
                scheme,
                'encoded_checksum_size',
                scheme.checksum_size
            )
            max_lengths.append(length)

        return max(max_lengths)

    def load_dialect_impl(self, dialect):
        if dialect.name == 'postgresql':
            impl = postgresql.BYTEA(self.length)
        elif dialect.name == 'oracle':
            impl = oracle.RAW(self.length)
        elif dialect.name == 'sqlite':
~~            impl = sqlite.BLOB(self.length)
        else:
            impl = types.VARBINARY(self.length)
        return dialect.type_descriptor(impl)

    def process_bind_param(self, value, dialect):
        if isinstance(value, Password):
            if value.secret is not None:
                return self._hash(value.secret).encode('utf8')

            return value.hash

        if isinstance(value, six.string_types):
            return self._hash(value).encode('utf8')

    def process_result_value(self, value, dialect):
        if value is not None:
            return Password(value, self.context)

    def _hash(self, value):
        return getattr(self.context, self.hashing_method)(value)

    def _coerce(self, value):

        if value is None:


## ... source file continues with no further sqlite examples...

```

