# Google Ml kit Plugin

![Pub Version](https://img.shields.io/pub/v/google_ml_kit)

Flutter plugin to use [google's standalone ml kit](https://developers.google.com/ml-kit) for Android .
<img src="screenshots/pose.png" > <img src="screenshots/imagelabeling.png"> <img src="screenchots/barcode.png">

## Currently supported api's
* [Pose Detection](https://developers.google.com/ml-kit/vision/pose-detection)
* [Image Labelling](https://developers.google.com/ml-kit/vision/image-labeling)
* [Barcode Scanning](https://developers.google.com/ml-kit/vision/barcode-scanning)

Support for other api's will be shortly added

## Usage
Add this plugin as dependency in your pubspec.yaml.

* In your project-level build.gradle file, make sure to include Google's Maven repository in both your buildscript and allprojects sections(for all api's).
* Add the dependencies for the ML Kit Android libraries to your module's app-level gradle file.
* For api's which require models choose either to bundle model with your app or using model within google play service.

## First Create an InputImage

Prepare Input Image(image you want to process)
```
import 'package:google_ml_kit/google_ml_kit.dart';

final inputImage = InputImage.fromFilePath(filePath);

// Or you can prepare image form
//final inputImage = InputImage.fromFile(file);

// final inputImageData = InputImageData(
//     size: size of image,
//     rotation: roatation degree(0,90,180,270 supported),
//     inputImageFormat:InputImageFormat.NV21 (default format of image);
//         
//
// var inputImage = InputImage.fromBytes(
//     bytes: await pickedFile.readAsBytes(), 
//     inputImageData: inputImageData);
```

To know more about [formats of image](https://developer.android.com/reference/android/graphics/ImageFormat.html#NV21).

## Create an instance of detector
## Call `processImage()` to obtain the result

## Pose Detection

-**In you app-level build.gradle. For latest version check [Pose Detection](https://developers.google.com/ml-kit/vision/pose-detection)**
```
dependencies {
  // If you want to use the base sdk
  implementation 'com.google.mlkit:pose-detection:latest version'
  // If you want to use the accurate sdk
  implementation 'com.google.mlkit:pose-detection-accurate:latest version'
}
```
If you choose google service way.In **app level buil.gradle add**

```
<application ...>
        ...
      <meta-data
          android:name="com.google.mlkit.vision.DEPENDENCIES"
          android:value="ica" />
      <!-- To use multiple models: android:value="ica,model2,model3" -->
      </application>
```
**The same implies for all other models as well**


-**Create [`PoseDetectorOptions`]()**
```
final options = PoseDetectorOptions(
        poseDetectionModel: PoseDetectionModel.BasePoseDetector,
        selectionType : LandmarkSelectionType.all,
        poseLandmarks:(list of poseaLndmarks you want); 
//or PoseDetectionModel.AccuratePoseDetector to use accurate pose detector
        
```
-**Obtain [`PoseDetector`] instance.**

**Note**: To obtain default poseDetector no options need to be specied. It gives all available landmarks using BasePoseDetector Model.

**The same implies to other detectors as well**
```
PoseDetector poseDetector = GoogleMlKit.instance
                               .poseDetector([PoseDetectorOptions options]);
```
-Call `processImage(InputImage inputImage)` to obtain the result.
It returns 
>Map<int,[PoseLandMark]()>
```
final landMarksMap = await poseDetector.processImage(inputImage);
```
Use the map to extract data. See this [example]("example/lib/DetectorViews/pose_detector_view.dart") to get better idea.

## Image Labeling
**In you app-level build.gradle. For latest version check [Image Labeling](https://developers.google.com/ml-kit/vision/image-labeling/)**
```
dependencies {
       // ...
// Use this dependency to bundle the model with your app
      implementation 'com.google.mlkit:image-labeling:17.0.0'
// Use this dependency to use dynamically downloaded model in Google Play Service
      implementation 'com.google.android.gms:play-services-mlkit-image-labeling:16.0.0'
    }
```
**Create `ImageLabelerOptions`**. This uses [google's base model](https://developers.google.com/ml-kit/vision/image-labeling/label-map)
```
final options =ImageLabelerOptions( confidenceThreshold = confidenceThreshold);
// Default =0.5
//lies between 0.0 to 1.0
        
```
To use custom **tflite** models
```
CustomImageLabelerOptions options = CustomImageLabelerOptions(
        customModel: CustomTrainedModel.asset 
       (or CustomTrainedModel.file),// To use files stored in device
        customModelPath: "file path");
```
To use **autoMl vision models** models
```
final options = AutoMlImageLabelerOptions(
      customTrainedModel:, 
      customModelPath:);
```
**Obtain [`ImageLabeler`]() instance.**
```
ImageLabeler imageLabeler = GoogleMlKit.instance.imageLabeler([options]);
```
**call `processImage()`**
It returns List<[ImageLabel]()>
```
final labels = await imageLabeler.processImage(inputImage);
```
**To know more see this [example]("example/lib/DetectorViews/label_detector_view.dart.dart")**

## Barcode Scanner
**In you app-level build.gradle. For latest version check [Barcode Scanning](https://developers.google.com/ml-kit/vision/barcode-scanning)**
```
dependencies {
      // ...
// Use this dependency to bundle the model with your app
      implementation 'com.google.mlkit:barcode-scanning:16.0.3'
// Use this dependency to use the dynamically downloaded model in Google Play Services
      implementation 'com.google.android.gms:play-services-mlkit-barcode-scanning:16.1.2'
    }
```
**Obtain [`BarcodeScanner`]() instance.**
```
BarcodeScanner barcodeScanner = GoogleMlKit.instance
                                           .barcodeScanner(
                                           formats:(List of BarcodeFormats);

```
Supported [BarcodeFormat](https://developers.google.com/android/reference/com/google/mlkit/vision/barcode/Barcode.BarcodeFormat)s .Access them using 

>Barcode.FORMAT_Default

>Barcode.FORMAT_Code_128

etc..

**call `processImage()`**
It returns List<[Barcode]()>
```
final result = await barcodeScanner.processImage(inputImage);
```
To know more see this [example]("example/lib/DetectorViews/barcode_scanner_view.dart")

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
