GMT的单位
=========

:Create Date: 2015-02-01
:Last Updated: 2015-02-01

GMT中的单位分为两类，即长度单位和距离单位。需要注意这两者的区别，长度单位一般用于度量纸张上的距离，而距离单位用于度量真实地球上的距离。

长度单位
--------

GMT中可以使用的长度单位包括厘米（\ **c**\ ）、英寸（\ **i**\ ）和点（\ **p**\ ）。它们之间的关系如下::

    1 inch = 2.54 cm = 72 point

GMT的配置参数\ :ref:`PROJ_LENGTH_UNIT <PROJ_LENGTH_UNIT>`\ 定义了默认情况下所使用的单位。当给定了某个长度量，但未显式指定长度单位时，则使用\ :ref:`PROJ_LENGTH_UNIT <PROJ_LENGTH_UNIT>`\ 来对该长度量进行解释。

你可以使用::

    gmt gmtget PROJ_LENGTH_UNIT

来检查GMT当前的默认长度单位，也可以使用::

    gmt gmtset PROJ_LENGTH_UNIT inch

来修改默认的长度单位。

除此之外，还可以显式地指定当前长度量要使用的单位。比如\ **-X4**\ 会由于PROJ_LENGTH_UNIT的不同而被解释为4厘米、4英尺或4点，而\ **-X4c**\ 则只会被解释为4厘米。

因而，对于长度单位的使用，有如下建议：

- 为全部长度量显式指定单位，不依赖于系统的默认设置；
- p用于指定较小的长度量，比如线宽；
- c和i用于指定较大的长度量，比如坐标原点的移动距离；
- 尽量使用国际单位制（c）而不用美国单位制（i），因为国人对于1厘米要比1英寸更有概念；

距离单位
--------

常用的距离单位包括：

- **d**: 弧度（degree of arc）；
- **m**: 弧分（minute of arc）；
- **s**: 弧秒（second of arc）；
- **k**: 千米（kilometer）；
- **e**: 米（meter）；默认值；

不常用的距离单位包括：

- **f**: 英尺（foot）；
- **M**: Statute mile；
- **n**: Nautical mile；
- **u**: US Survey foot；

对于笛卡尔数据以及笛卡尔坐标而言，数据的单位是千克还是牛顿并不重要，也不需要显示指定。对于地理数据则不一样，因为距离可以用多种不同的单位来指定。GMT中某些可以接收真实地球距离作为参数的选项，比如搜索半径或距离等，可以处理不同的单位需求。比如若搜索半径为\ ``30``\ ，则根据默认的距离解释为30米，若搜索半径为\ ``30k``\ ，则解释为30千米。

距离的计算
----------

距离的计算依赖于地球椭率的选取（\ :ref:`PROJ_ELLIPSOID <PROJ_ELLIPSOID>`\ ）以及具体的计算方法。GMT提供了三种计算方法，在精度和计算效率上各有权衡。

Flat Earth距离
^^^^^^^^^^^^^^

地球上任意两点A和B的距离可以通过如下公式近似计算得到：

.. math::

	 d_f = R \sqrt{(\theta_A - \theta_B)^2 + \cos \left [ \frac{\theta_A +
	 \theta_B}{2} \right ] \Delta \lambda^2}, \label{eq:flatearth}

其中R是地球的平均半径，\ :math:`\theta`\ 是纬度，经度差\ :math:`\Delta \lambda = \lambda_A - \lambda_B`\ 。式中，地理坐标的单位是弧度。

该方法适用于纬度相差不大并且对计算效率要求比较高的情况。在距离前加上前缀\ **-**\ 即表示此距离用Flat Earth方法计算。在某些情况下，只需要指定距离的单位而不指定具体的距离值，则可以在距离单位前加上前缀\ **-**\ 表示该距离用Flat Earth方法计算。

比如，\ ``-S-50M``\ 表示设定搜索半径为50海里，其中距离用Flat Earth方法计算。

大圆路径距离
^^^^^^^^^^^^

这是默认的计算距离的方法，球面上两点A和B的距离用Haversine公式计算：

.. math::

	 d_g = 2R \sin^{-1}  {\sqrt{\sin^2\frac{\theta_A - \theta_B}{2} + \cos
	 \theta_A \cos \theta_B \sin^2 \frac{\lambda_A - \lambda_B}{2}} },
	 \label{eq:greatcircle}

该方法将地球近似为一个半径为\ **R**\ 的球，适用于大多数的情况。比如，\ ``-S5000f``\ 设定了用该方法计算搜索半径5000英寸。

注意：GMT默认参数中有两个参数可以控制距离的计算。

- :ref:`PROJ_MEAN_RADIUS <PROJ_MEAN_RADIUS>`\ ：平均半径；
- :ref:`PROJ_AUX_LATITUDE <PROJ_AUX_LATITUDE>`\ ：将大地测量纬度转换为多个适合球近似的辅助纬度。

大地测量距离
^^^^^^^^^^^^

使用Vincenty的完全椭球公式计算最精确的距离。可以通过在距离或距离单位前加上前缀\ ``+``\ 来指定用该方法计算距离。比如，\ ``-S+20k``\ 表示用该方法计算的20千米的距离。
