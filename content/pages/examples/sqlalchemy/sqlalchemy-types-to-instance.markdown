title: sqlalchemy.types to_instance Example Code
category: page
slug: sqlalchemy-types-to-instance-examples
sortorder: 500031131
toc: False
sidebartitle: sqlalchemy.types to_instance
meta: Python example code that shows how to use the to_instance callable from the sqlalchemy.types module of the SQLAlchemy project.


to_instance is a callable within the sqlalchemy.types module of the SQLAlchemy project.


## Example 1 from GeoAlchemy2
[GeoAlchemy2](https://github.com/geoalchemy/geoalchemy2)
([project documentation](https://geoalchemy-2.readthedocs.io/en/latest/)
and
[PyPI package information](https://pypi.org/project/GeoAlchemy2/))
extends [SQLAlchemy](/sqlalchemy.html) with new data types for working
with geospatial databases, particularly [PostGIS](http://postgis.net/),
which is a spatial database extender for [PostgreSQL](/postgresql.html).
The project is provided as open source under the
[MIT license](https://github.com/geoalchemy/geoalchemy2/blob/master/COPYING.rst).

[**GeoAlchemy2 / geoalchemy2 / elements.py**](https://github.com/geoalchemy/geoalchemy2/blob/master/geoalchemy2/./elements.py)

```python
# elements.py
import binascii
import struct

try:
    from sqlalchemy.sql import functions
    from sqlalchemy.sql.functions import FunctionElement
except ImportError:  # SQLA < 0.9  # pragma: no cover
    from sqlalchemy.sql import expression as functions
    from sqlalchemy.sql.expression import FunctionElement
~~from sqlalchemy.types import to_instance
from sqlalchemy.ext.compiler import compiles

from .compat import PY3, str as str_
from .exc import ArgumentError


if PY3:
    BinasciiError = binascii.Error
else:
    BinasciiError = TypeError


class HasFunction(object):
    pass


class _SpatialElement(HasFunction):

    def __init__(self, data, srid=-1, extended=False):
        self.srid = srid
        self.data = data
        self.extended = extended

    def __str__(self):


## ... source file abbreviated to get to to_instance examples ...


        try:
            bin_data = binascii.unhexlify(data[:114])
        except BinasciiError:
            bin_data = data
            data = str(binascii.hexlify(data).decode(encoding='utf-8'))
        byte_order = bin_data[0]
        srid = bin_data[53:57]
        if not PY3:
            byte_order = bytearray(byte_order)[0]
        srid = struct.unpack('<I' if byte_order else '>I', srid)[0]
        _SpatialElement.__init__(self, data, srid, True)

    @property
    def desc(self):
        return self.data

    @staticmethod
    def _data_from_desc(desc):
        return desc


class CompositeElement(FunctionElement):

    def __init__(self, base, field, type_):
        self.name = field
~~        self.type = to_instance(type_)

        super(CompositeElement, self).__init__(base)


@compiles(CompositeElement)
def _compile_pgelem(expr, compiler, **kw):
    return '(%s).%s' % (compiler.process(expr.clauses, **kw), expr.name)



## ... source file continues with no further to_instance examples...

```

