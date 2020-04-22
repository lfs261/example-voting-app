## Creating a dev environment for vote python app

Follow these steps to build and run this app,

	* Install Python (2.7) with PIP
	* Checkout the code and change into vote dir
	* Run "pip install -r requirements.txt"
	* Run "nosetests -v" to run unit tests
	* Run "gunicorn app:app -b 0.0.0.0:80" to run de app
	* Application would start on port 80 as specified above
