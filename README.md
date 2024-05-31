# Face Mask Detection Application

## Introduction

The Face Mask Detection Application is an Android-based machine learning app designed to detect whether individuals are wearing face masks in real-time using their device's camera. This application leverages modern camera and machine learning technologies to provide an efficient and accurate solution for face mask detection, crucial in today's health-conscious environment.

## Problem Definition

The COVID-19 pandemic has highlighted the importance of wearing face masks to prevent the spread of the virus. Manual monitoring of face mask compliance is labor-intensive and not always feasible. An automated, real-time detection system is necessary to ensure compliance and safety in public spaces.

## Key Features

- **Real-time Detection**: Utilizes the device's camera to detect face masks in real-time.
- **Dual Camera Support**: Supports both front and back cameras.
- **Visual Feedback**: Provides immediate visual feedback indicating the presence or absence of a face mask.
- **Offline Capability**: Runs completely offline without the need for a network connection.
- **User-Friendly Interface**: Simple and intuitive user interface for easy operation.

## Architecture & Detailed Working

### Technology Stack

- **Kotlin**: Kotlin is the primary programming language used for developing this Android application. Kotlin is known for its concise syntax, safety features, and full interoperability with Java. It offers a modern development experience and reduces the amount of boilerplate code, making the development process more efficient and error-free.

- **Android Studio**: Android Studio is the Integrated Development Environment (IDE) used for building the application. It provides a robust set of tools for coding, debugging, and testing Android apps. Android Studio's Gradle-based build system simplifies dependency management and supports advanced build configurations.

- **CameraX**: CameraX is a Jetpack library that provides a consistent and easy-to-use API for integrating camera functionality in Android applications. It abstracts away the complexities of the camera and camera2 APIs, making it simpler to develop camera-based features. CameraX supports backward compatibility down to Android 5.0 (Lollipop).

  Key components of CameraX used in this application include:

  - **CameraProvider**: Manages the lifecycle of camera use cases, such as preview, image capture, and image analysis.
  - **CameraSelector**: Configures which camera (front or back) to use.
  - **Preview**: Displays the camera feed on the screen.
  - **ImageAnalysis**: Analyzes frames in real-time for further processing by the machine learning model.

- **TensorFlow Lite**: TensorFlow Lite is a lightweight machine learning library designed for mobile and embedded devices. It enables running machine learning models on-device, providing low-latency inference without the need for a network connection. TensorFlow Lite supports a variety of models and optimizations to enhance performance on mobile hardware.

  Key components of TensorFlow Lite used in this application include:

  - **Model Loading**: The face mask detection model is loaded into the application.
  - **Inference**: The loaded model processes camera frames and outputs the probability of the presence of a face mask.
  - **Result Handling**: The application interprets the model's output and updates the UI accordingly.

- **Gradle**: Gradle is the build automation tool used in Android Studio for managing dependencies, configuring build options, and automating the build process. Gradle scripts define the project's build configurations and handle tasks such as compiling code, packaging APKs, and running tests.

### Detailed Working

#### CameraX Integration

1. **ExecutorService**: An `ExecutorService` is used to handle background tasks such as processing bitmap images and analyzing frames. This ensures that the main UI thread is not blocked, providing a smooth user experience.

2. **ProcessCameraProvider**: The `ProcessCameraProvider` manages the lifecycle of the camera and provides access to camera features. It is responsible for checking the availability of cameras and binding them to the lifecycle of the activity.

3. **ImageAnalysis.Analyzer**: The `ImageAnalysis.Analyzer` interface is implemented to analyze each frame from the camera feed. Frames are converted to bitmap format and passed to the TensorFlow Lite model for inference.

```kotlin
private fun setupCamera() {
    val cameraProviderFuture = ProcessCameraProvider.getInstance(this)

    cameraProviderFuture.addListener(Runnable {
        cameraProvider = cameraProviderFuture.get()
        lensFacing = when {
            hasFrontCamera -> CameraSelector.LENS_FACING_FRONT
            hasBackCamera -> CameraSelector.LENS_FACING_BACK
            else -> throw IllegalStateException("No cameras on this device")
        }
        setupCameraControllers()
        setupCameraUseCases()
    }, ContextCompat.getMainExecutor(this))
}
```

#### TensorFlow Lite Integration

1. **Model Loading**: The pre-trained face mask detection model is loaded into the application using TensorFlow Lite. The model is optimized for mobile devices to ensure efficient and fast inference.

2. **Inference**: Each frame captured by the camera is processed by the TensorFlow Lite model to determine the presence of a face mask. The model outputs the probability scores for different classes (e.g., "mask" and "no mask").

3. **Result Handling**: Based on the output of the model, the application updates the UI to indicate whether a mask is detected. This includes changing text labels, colors, and progress bars to provide immediate feedback to the user.

```kotlin
private fun setupML() {
    val options = Model.Options.Builder().setDevice(Model.Device.GPU).setNumThreads(5).build()
    faceMaskDetection = FackMaskDetection.newInstance(applicationContext, options)
}

private fun setupMLOutput(bitmap: Bitmap) {
    val tensorImage = TensorImage.fromBitmap(bitmap)
    val result = faceMaskDetection.process(tensorImage)
    val output = result.probabilityAsCategoryList.apply {
        sortByDescending { it.score }
    }
    lifecycleScope.launch(Dispatchers.Main) {
        output.firstOrNull()?.let { category ->
            updateUI(category)
        }
    }
}
```

### UI Components

- **Camera Preview**: The `Preview` use case displays the live camera feed on the screen.
- **Detection Feedback**: The UI updates based on detection results, changing text labels and progress bars to indicate whether a mask is detected.

## Conclusion

The Face Mask Detection Application is a robust tool for ensuring face mask compliance. By combining CameraX and TensorFlow Lite, the application offers real-time detection with high accuracy, operating entirely offline. This makes it a valuable solution for public safety in various environments.

## Future Scope of Project

- **Enhanced Model Accuracy**: Continuously improve the detection model with more diverse and comprehensive datasets.
- **Multi-face Detection**: Extend the capability to detect multiple faces and their mask statuses simultaneously.
- **Platform Expansion**: Develop versions of the application for other mobile platforms such as iOS.
- **Additional Features**: Integrate features like alert notifications and data logging for compliance monitoring.

## Bibliography

1. [CameraX Documentation](https://developer.android.com/training/camerax)
2. [TensorFlow Lite Documentation](https://www.tensorflow.org/lite)
3. [Kotlin Programming Language](https://kotlinlang.org/)
4. [Android Studio](https://developer.android.com/studio)
5. [Gradle Build Tool](https://gradle.org/)
6. [ActivityResultContract](https://developer.android.com/reference/androidx/activity/result/contract/ActivityResultContract)

---

This documentation provides a comprehensive overview of the Face Mask Detection Application, covering all key aspects from problem definition to future scope, ensuring a clear understanding of the project's objectives and workings.

> Created By Snaptokon 🙏🏻
