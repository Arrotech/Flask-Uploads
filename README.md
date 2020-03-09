# Uploading Files¶


The basic idea of file uploads is actually quite simple. It basically works like this:

A `<form>` tag is marked with enctype=multipart/form-data and an `<input type=file>` is placed in that form.

The application accesses the file from the files dictionary on the request object.

use the `save()` method of the file to save the file permanently somewhere on the filesystem.

Install the following depedencies:

    pip install flask

Create a new `__init__.py` file add add the following code:

    import os
    from flask import Flask, flash, request, redirect, url_for, render_template, send_from_directory
    from werkzeug.utils import secure_filename

    UPLOAD_FOLDER = '/home/arrotech/Projects/Flask-Uploads/uploads'
    ALLOWED_EXTENSIONS = {'pdf', 'txt', 'png', 'jpg', 'jpeg', 'gif'}

    app = Flask(__name__, template_folder='../templates')
    app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
    app.config['SECRET_KEY'] = 'this is secret'
    app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024

    def allowed_file(filename):
        return '.' in filename and \
            filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS

    @app.route('/', methods=['GET','POST'])
    def upload_file():
        if request.method == 'POST':
            # check if the post request has the file part
            if 'file' not in request.files:
                flash('No file part')
                return redirect(request.url)
            file = request.files['file']
            print(file)
            # if user does not select file, browser also
            # submit an empty part without filename
            if file.filename == '':
                flash('No selected file')
                return redirect(request.url)
            if file and allowed_file(file.filename):
                filename = secure_filename(file.filename)
                print(filename)
                file.save(os.path.join(UPLOAD_FOLDER, filename))
                return redirect(url_for('uploaded_file', filename=filename))

        return render_template('upload.html')

    @app.route('/uploads/<filename>')
    def uploaded_file(filename):
        return send_from_directory(UPLOAD_FOLDER, filename)

Create an upload.html file and add te following code:

    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Upload new File</title>
    </head>

    <body>
        <h1>Upload new File</h1>
        <form method=post enctype=multipart/form-data> <input type=file name=file>
            <input type=submit value=Upload>
        </form>
    </body>

    </html>

Finally create run.py file and add te following code and run te application:

    from app import app

    if __name__ == "__main__":
        app.run(debug=True)