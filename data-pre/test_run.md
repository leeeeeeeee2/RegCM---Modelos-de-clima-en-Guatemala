构建docker的图像。 

```
docker build -t "regcm:4.7.6" .
```


Correr contenedor Docker:

```
docker run -it -v ~/DATOS/RegCM:/data --name regcm regcm:4.7.6 bash
```

这将创建一个名为 "regcm "的docker容器，以交互式模式运行bash。在那里你可以利用该软件。你可以用 "exit "退出，要重新进入容器中的控制台，你可以使用这个命令。

```
docker start -ai regcm
```


这个脚本的描述见于[这里]的用户指南(https://gforge.ictp.it/gf/download/docmanfileversion/97/1690/UserGuide.pdf)。在运行这个之前，有必要下载数据，如test_data.md中所述。

```
export REGCM_GLOBEDAT=/data
export REGCM_ROOT=/opt/RegCM-4.7.6

cd $REGCM_GLOBEDAT

mkdir RUNS
cd RUNS
mkdir RUN01
export REGCM_RUN=/data/RUNS/RUN01/

cd $REGCM_RUN
mkdir input output
cp $REGCM_ROOT/Testing/test_001.in .

chmod  -R 777 $REGCM_RUN
```

编辑test_001.in，把数据、输入和输出目录放在指南中描述的位置。将test_001.in中带有参数 "cftomax "的一行注释掉。


```
...
 dirter = 'input/',
 inpter = '/data/',
...
 dirglob = 'input/',
 inpglob = '/data/',
...
 dirout='output/'
...
```

Estando en el directorio $REGCM_RUN Preparar los datos:

```
$REGCM_ROOT/bin/terrain test_001.in
$REGCM_ROOT/bin/sst test_001.in
$REGCM_ROOT/bin/icbc test_001.in
```

现在，我在openmpi中禁用了MCA，以避免错误，并在4个cpus下运行（如果你的电脑有不同数量的cpus，就用它代替4个）。

```
export OMPI_MCA_btl_vader_single_copy_mechanism=none
mpirun -np 4 $REGCM_ROOT/bin/regcmMPI test_001.in   
```
