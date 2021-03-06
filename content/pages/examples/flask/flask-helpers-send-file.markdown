title: flask.helpers send_file Example Code
category: page
slug: flask-helpers-send-file-examples
sortorder: 500021021
toc: False
sidebartitle: flask.helpers send_file
meta: Python example code that shows how to use the send_file callable from the flask.helpers module of the Flask project.


[send_file](https://github.com/pallets/flask/blob/master/src/flask/helpers.py)
is function in the [Flask](/flask.html) `flask.helpers` module.
`send_file` transfers the contents of a file to the client using the most
efficient method available and configured in the Flask settings. It
attempts to guess the correct mimetype to use but it can also be
explicitly configured.

Note that `send_file` is usually imported directly from `flask` instead of
from `flask.helpers`, even though it is defined within the `helpers` module.
It's the same function that is imported, but it's less characters to type
when you leave off the `.helpers` part.


## Example 1 from Flask-VueJs-Template
[Flask-VueJs-Template](https://github.com/gtalarico/flask-vuejs-template)
([demo site](https://flask-vuejs-template.herokuapp.com/))
is a minimal [Flask](/flask.html) boilerplate starter project that
combines Flask, [Vue.js](https://www.fullstackpython.com/vuejs.html),
and [Flask-RESTPlus](https://flask-restplus.readthedocs.io/en/stable/).
The project provides some sensible defaults that are easy to continue
building on, and the source code is open source under the
[MIT license](https://github.com/gtalarico/flask-vuejs-template/blob/master/LICENSE.md).

[**Flask-VueJs-Template / app / __init__.py**](https://github.com/gtalarico/flask-vuejs-template/blob/master/app/./__init__.py)

```python
# __init__.py
import os
~~from flask import Flask, current_app, send_file

from .api import api_bp
from .client import client_bp

app = Flask(__name__, static_folder='../dist/static')
app.register_blueprint(api_bp)

from .config import Config
app.logger.info('>>> {}'.format(Config.FLASK_ENV))

@app.route('/')
def index_client():
    dist_dir = current_app.config['DIST_DIR']
    entry = os.path.join(dist_dir, 'index.html')
~~    return send_file(entry)





## ... source file continues with no further send_file examples...

```

