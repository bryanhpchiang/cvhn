# cvhn

Complex Valued Holographic Networks

## Dependencies

First, install JAX with GPU support enabled.

```sh
pip install --upgrade jax jaxlib==0.1.62+cuda110 -f https://storage.googleapis.com/jax-releases/jax_releases.html
```

Depending on your CUDA version, you might need to change to `+cuda111` or `+cuda112`. All versions are listed here (https://storage.googleapis.com/jax-releases/jax_releases.html), scroll down for the most recent CUDA releases.

Install Torch following their official instructions: https://pytorch.org/get-started/locally/. For example:

```sh
pip install torch==1.8.0+cu111 torchvision==0.9.0+cu111 torchaudio==0.8.0 -f https://download.pytorch.org/whl/torch_stable.html
```

Install the rest of the requirements.

```sh
pip install -r requirements.txt
```

## Usage

Note: the dataset has not been published and as a result is not publicly available. You can find more details about the dataset inside our paper.

Find the root location of the dataset. It should contain a folder called `holography_training_data`.

```
readlink -f /path/to/data
```

In `common.sh`, set the location of `prefix` to the root location of your dataset.

### Training

Training happens with `train.py`. This creates a network of the specified type and trains is over the training set with periodic evaluations on the validation set.

```sh
source common.sh
python train.py --phase_path "$phase_path" --captured_path "$captured_path" \
    --target_network "complexcnnc" \
    --experiment exp6_trial2 \
    --activation real_relu \
    --optimizer complex_adam
    --lr_model 5e-5
```

A full list of options and their descriptions are available in `train_helper.py`.

You can see a list of different training commands in `train.sh`.

The three types of networks are:

- `cnnr`: Real network that operates on the amplitude of the phase.
- `stackedcnnc`: Convert complex phase to real, 2-channel representation.
- `complexcnnc`: Treat phase as complex, entire network has complex valued weights and biases.

### Evaluation

Evaluation happens with `evaluate.py`. This script runs a model (provided via `pretrained_path`) over the test set.

Here is a sample evaluation command:

```sh
source common.sh
pretrained_path="models/green_exp6_trial2_Targetcomplexcnnc-Activationreal_relu-Norminstance_LossL1_lr0.0005_Optimizercomplex_adam_model_iter8001_1epoch.pth"
python evaluate.py --phase_path "$phase_path" --captured_path "$captured_path" \
    --target_network "complexcnnc" \
    --experiment exp6_trial2 \
    --activation real_relu \
    --pretrained_path "$pretrained_path" \
    --optimizer complex_adam
```

You can see a full list of different evaluation commands in `evaluation.sh`.

### Artifacts

Models will be saved to the `models/` folder, and TensorBoard logging will be written to `JAX_runs/`. You can start TensorBoard with the following command;

```sh
make tb
```

### Code Structure

- `optimize.py` contains our custom complex optimizers.
- `phase_capture_loader.py` contains the Torch DataLoader for running the dataset.
- `complex_activations.py` contains the complex activations functions for ComplexCNNC.
- `asm.py` contains a JAX implementation of the angular spectrum method for free-space propagation.

## Contact

The authors are Manu Gopakumar (manugopa@stanford.edu) and Bryan Chiang (bhchiang@stanford.edu).
