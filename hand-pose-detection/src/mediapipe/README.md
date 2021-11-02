# MediaPipeHands

MediaPipeHands-MediaPipe wraps the MediaPipe JS Solution within the familiar
TFJS API [mediapipe.dev](https://mediapipe.dev).

--------------------------------------------------------------------------------

## Table of Contents

1.  [Installation](#installation)
2.  [Usage](#usage)
3.  [Keypoint Diagram](#keypoint-diagram)

## Installation

To use MediaPipeHands:

Via script tags:

```html
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands"></script>
<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/hand-pose-detection"></script>
```

Via npm:
```sh
yarn add @tensorflow-models/hand-pose-detection
yarn add @mediapipe/hands
```

-----------------------------------------------------------------------
## Usage

If you are using the hand-pose-detection API via npm, you need to import the libraries first.

### Import the libraries

```javascript
import * as handPoseDetection from '@tensorflow-models/hand-pose-detection';
import '@mediapipe/hands';
```

### Create a detector

Pass in `handPoseDetection.SupportedModels.MediaPipeHands` from the
`handPoseDetection.SupportedModel` enum list along with a `detectorConfig` to the
`createDetector` method to load and initialize the model.

`detectorConfig` is an object that defines MediaPipeHands specific configurations for `MediaPipeHandsMediaPipeModelConfig`:

*   *runtime*: Must set to be 'mediapipe'.

*   *maxHands*: Defaults to 2. The maximum number of hands that will be detected by the model. The number of returned hands can be less than the maximum (for example when no hands are present in the input).

*   *modelType*: specify which variant to load from `MediaPipeHandsModelType` (i.e.,
    'lite', 'full'). If unset, the default is 'full'.

*   *solutionPath*: The path to where the wasm binary and model files are located.

```javascript
const model = handPoseDetection.SupportedModels.MediaPipeHands;
const detectorConfig = {
  runtime: 'mediapipe',
  solutionPath: 'base/node_modules/@mediapipe/hands'
};
detector = await handPoseDetection.createDetector(model, detectorConfig);
```

### Run inference

Now you can use the detector to detect hand poses. The `estimateHands` method
accepts both image and video in many formats, including:
`HTMLVideoElement`, `HTMLImageElement`, `HTMLCanvasElement`. If you want more
options, you can pass in a second `estimationConfig` parameter.

`estimationConfig` is an object that defines MediaPipeHands specific configurations for `MediaPipeHandsMediaPipeEstimationConfig`:

*   *flipHorizontal*: Optional. Defaults to false. When image data comes from camera, the result has to flip horizontally.

The following code snippet demonstrates how to run the model inference:

```javascript
const estimationConfig = {flipHorizontal: false};
const hands = await detector.estimateHands(image, estimationConfig);
```

The returned hands list contains a single element for each detected hand in the image (upto maxHands).

For each hand, the structure contains a prediction of the handedness (left or right) as well as a confidence score of this prediction. An array of keypoints is also returned.
Each keypoint contains x, y, and name.

Example output:
```
[
  {
    score: 0.8,
    Handedness: ‘Right’,
    keypoints: [
      {x: 105, y: 107, name: "wrist"},
      {x: 108, y: 160, name: "pinky_tip"},
      ...
    ]
  }
]
```
The name provides a label for each keypoint, such as 'wrist', 'pinky_tip', etc.
