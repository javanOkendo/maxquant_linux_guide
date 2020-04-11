Guide on how to Run MaxQuant at the University of Cape Town High Performance Computing (HPC) cluster 
====================================================================================================
This workflow was adapted from [Albert Chen blog](http://atchen.me/research/2019/03/21/mq-linux.html) and we have modify it to run the MaxQuant sequence database searches for our TB Mass spectrometry data.

The goal of the `gen_mqpar.py` script is to create MaxQuant configuration files (`mqpar.xml` files) in a console environment without having to transfer one over or edit the XML manually. The example given has some hard-coded stuff and makes assumptions about your filesystem, so please follow the steps below to get started.

1 Verify that [Mono](https://www.mono-project.com/download/stable/#download-lin) is installed.

2. Create template `mqpar.xml` files – representing configurations for MaxQuant searches you routinely run on your experiments. There are a bunch of templates in,`templates/` directory for easy access. 
    - It is recommended that you generate the `parameres` file first in the MaxQuant GUI, as it's easier and you can verify that the configuration file is valid before moving it to your Linux instance.
    - Don't worry about the raw files, folder paths, or FASTA file paths in the template file – they will be replaced by the script
3. Move the MaxQuant files onto your Linux instance. Then add the MaxQuant `CMD` executable to your environment `path` by adding `export MQ_1_7_0=~/MQ_1_7_0/bin/MaxQuantCmd.exe`to the `.bash_profile`. You can manage your MaxQuant versions however you like, just make sure that it's reflected in the `gen_mqpar.py` script.
4. Move your protein FASTA files over to the Linux instance. The current `gen_mqpar.py` script has the path to the FASTA file hard-coded, and it's straightforward to change this or make it argument-driven.

Run the following code to create `mqpar.xml`:
```bash
./gen_mqpar.py <path/to/template> <path/to/raw_file_folder> -o <path/to/output_mqpar.xml> -t <num_threads>
# for example,
./gen_mqpar.py templates/SILAC.xml raw_files/FP93 -o fp93_silac.xml -t 6
```
> Personally I prefer creating the parameter file using the MaxQuant GUI because it is easier and faster.

5. Set un your script with the job instructions as shown:
```
#!/bin/bash
#SBATCH --account=cbio
#SBATCH --partition=ada
#SBATCH --nodes=1 --ntasks=40
#SBATCH --time=170:00:00
#SBATCH --job-name="maxquantrun"
#SBATCH --mail-user=javanokendo@gmail.com
#SBATCH --mail-type=END,FAIL

# Load the mono module
module load software/mono-6.8.0

cd /scratch/oknjav001/MaxQuant-1.6

mono /scratch/oknjav001/MaxQuant/bin/MaxQuantCmd.exe balddataparam.xml             
```
And to run this to your local machine, just do `mono /path/to/bin/MaxQuantCmd.exe balddateparam.xml`

Do not hesitate to [contact](javanokendo@gmail.com) me if you need further clarification.
