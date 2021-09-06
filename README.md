# Reproduce Pointnet2

Created: July 3, 2021 4:56 PM
Engineers: Xiaobo Liu, Runze Gan
Priority: P1 🔥
Sprint: Sprint 21
Status: In Progress
Type: Epic ⛰️

Create a new item in your database and choose `Bug` from the list of template options to generate the format below. Learn more about database templates [here](https://www.notion.so/Database-templates-454ed5ab5bd24226b58d176697bd7e10).

# Description

The experence of reproducing PointNet++

# The source:

[PointNet++](https://github.com/yanx27/Pointnet_Pointnet2_pytorch)

The data sets I download at the same github.

In docker to create a '**data**' floder in '**Pointnet_Pointnet2_pytorch'**  and put the dataset in there.

# The previous errors：

Readme writen by the author said the using environment is 'conda install pytorch==1.6.0 cudatoolkit=10.1 -c pytorch'. So I download in docker（pytorch/pytorch:1.6.0-cuda10.1-cudnn7-devel）from docker hub. 

But it will show that it can detect the data set and it won't continue training. Like the pictrues:

![Screenshot from 2021-08-25 22-30-10.png](Reproduce%20Pointnet2%2037080ccd11a04feaa70745af99f96c9f/Screenshot_from_2021-08-25_22-30-10.png)

![Screenshot from 2021-08-25 22-34-49.png](Reproduce%20Pointnet2%2037080ccd11a04feaa70745af99f96c9f/Screenshot_from_2021-08-25_22-34-49.png)

I watch my GPU's(watch -n 0.1 -d nvidia-smi ) the power and temperature still keeping unchanged which means the data are not transfered to GPU. 

I add some tips,like print("1") to see where it is stop. and I finded Cudnn is failed. For that,  

![Screenshot from 2021-08-26 13-47-12.png](Reproduce%20Pointnet2%2037080ccd11a04feaa70745af99f96c9f/Screenshot_from_2021-08-26_13-47-12.png)

I used a new pytorch docker(1.9.0-cuda11.1-cudnn8-devel) to try. It can works.

![Selection_003.png](Reproduce%20Pointnet2%2037080ccd11a04feaa70745af99f96c9f/Selection_003.png)

# Steps

## 1.Create the docker in the server

docker run -it --name="kaede" -p 8231:8231 -p 8232:8232 --gpus all pytorch/pytorch:1.9.0-cuda11.1-cudnn8-devel

('kaede' is docker's name; port depend on you) 

**remember add  --gpus all' otherwise it will show 'need driver'**

## 2.Clone the project from github

entry the docker: 

```python
cd
apt update
apt install git
git clone [https://github.com/yanx27/Pointnet_Pointnet2_pytorch](https://github.com/yanx27/Pointnet_Pointnet2_pytorch).git
```

now in you /root you will see a floder 'Pointnet_Pointnet2_pytorch'

## 3.Clone the date from PC to server and to docker

I downloaded the dataset in my PC firstly. So I need transfer it to the server and transfer it to the docker.

```docker
1. Transfer the local data set to the server
scp -r /home/kaede/Pointnet_Pointnet2_pytorch-master/data e231@172.16.127.89:/home/e231/Desktop
the first line is local address of data, the second line is the object address in server

2.Transfer the data set from the server to docker
docker cp ~/Desktop/data xxx(container address):/root/Pointnet_Pointnet2_pytorch
the line is you object address in docker
```

Now the data is in 'Pointnet_Pointnet2_pytorch'.

## 4.Train

```python
eg:
python train_classification.py --model pointnet2_cls_ssg --log_dir pointnet2_cls_ssg
```

If lack some packages, just 'pip install xxxxx'.

The more instructions are in [github](https://github.com/yanx27/Pointnet_Pointnet2_pytorch)'s readme.

# The summarize

It stands to reason that this process should be very simple as the README doing.

But it actually can't as follow when using pytorch==1.6.0 cudatoolkit=10.1 -c pytorch. But when using the pytorch:1.9.0-cuda11.1-cudnn8 is no problem.
