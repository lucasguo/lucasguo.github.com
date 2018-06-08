---
layout: post
category : Hadoop
tagline: MapReduce
tags : [Hadoop]
---
{% include JB/setup %}

MapReduce分成两部分 Map(可能还会有Combine），Reduce，整个操作流程都基于键值对

Source -> **Map** -> <K2,V2> -> **Combine** -> <K2,V2> -> **Reduce** -> Output

Map和Combine（可选）是在集群的单机中进行，Reduce是从集群中收集到各个单机产生的结果后做的操作。
Combine做的事情可能和Reduce是相同的，只是在不同的环境中执行，可以减少最后reduce时的工作量
MapReduce框架提供一些基础的Reducer，如IntSumReducer，LongSumReducer


```
Configuration conf = new Configuration();
Job job = Job.getInstance(conf, "job name");
job.setJarByClass(JobClass.class);
job.setMapperClass(JobMapper.class);
job.setCombinerClass(JobCombiner.class);
job.setReducerClass(JobReducer.class);
job.setOutputKeyClass(Text.class);
job.setOutputValueClass(IntWritable.class);
job.waitForCompletion(true);
```