# CHEMPROP v1.4.0

This setup is for running the v1.4.0 of ChemProp as a separate virtual env. Installing the chemprop module

````

module purge 
module load proxy-setup
module load python/3.8.5
python3 -m venv "/home/l017896/path...."

# Modules - make sure to have proxy-setup
pip install --upgrade pip
pip install numpy pandas matplotlib flask gunicorn hyperopt pandas-flavor scikit-learn
pip install rdkit-pypi
pip install typed-argument-parser 

# PyTorch 
pip3 install torch torchvision torchaudio #conflicts with cuda 
pip install torch==1.7.1+cu101 torchvision==0.8.2+cu101 torchaudio==0.7.2 -f https://download.pytorch.org/whl/torch_stable.html # for cuda 10.1 

# To install chemprop
git clone https://github.com/chemprop/chemprop.git
cd "path...../home/l017896/lrlhps_dir/repo/chemprop"
pip install -e .

# Jupyter Kernel
pip install ipykernel
python -m ipykernel install --user --name=chemprop_pgg


# To activate the env:
source "path...:lrlhps_dir/virtual_env/chemprop_pgg/bin/activate"
````

# To run chemprop_pgg use following setup:
````
module purge
module load proxy-setup
module load python/3.8.5
source ~path...../lrlhps_dir/virtual_env/chemprop_pgg/bin/activate
#
