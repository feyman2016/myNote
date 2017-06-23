## Flask Note

#### 1. How to modulize flask app

- Use Blueprint to seperate views and integrate views as module,example:

  ```
  mod = Blueprint('community', __name__, url_prefix='/community')


  @mod.route('/')
  def index():
      return render_template('community/index.html')


  @mod.route('/irc/')
  def irc():
      return render_template('community/irc.html')


  @mod.route('/badges/')
  def badges():
      return render_template('community/badges.html')
  ```

  then all request path started with `community` will be in this view.

  [Reference: Flask Web](https://github.com/pallets/flask-website)

- How to use flask to implement web-console ?

  the [stackoverflow answer](https://stackoverflow.com/questions/39520321/view-real-time-console-on-a-web-page-with-flask-python), this answer use python lib `socketio` to implement socket connection and use flask as normal web server.

  related [demo project](https://github.com/miguelgrinberg/Flask-SocketIO)

- ​