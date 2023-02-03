flutter_version=3.7.0

# Usage: make flutter_version=3.7.0 image
image:
	docker build --tag flutter_cicd_android:$(flutter_version) \
             --build-arg flutter_version=$(flutter_version) .
