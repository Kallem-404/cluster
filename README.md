# cluster
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<small><small><i>\n",
    "All the IPython Notebooks in **Clustering Algorithms** lecture series by **[Dr. Milaan Parmar](https://www.linkedin.com/in/milaanparmar/)** are available @ **[GitHub](https://github.com/milaan9/Clustering_Algorithms)**\n",
    "</i></small></small>"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Dimensionality Reduction"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {
    "ExecuteTime": {
     "end_time": "2021-09-18T15:40:28.921358Z",
     "start_time": "2021-09-18T15:40:21.087390Z"
    },
    "colab": {
     "base_uri": "https://localhost:8080/",
     "height": 104
    },
    "colab_type": "code",
    "executionInfo": {
     "elapsed": 5681,
     "status": "ok",
     "timestamp": 1546259198259,
     "user": {
      "displayName": "Bob Li",
      "photoUrl": "",
      "userId": "13034580972517925389"
     },
     "user_tz": -480
    },
    "id": "cb9XEtCKNnh-",
    "outputId": "04b24b82-bb55-4e98-b09f-5a32cf27fa3a"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Requirement already satisfied: sklearn in c:\\programdata\\anaconda3\\lib\\site-packages (0.0)\n",
      "Requirement already satisfied: scikit-learn in c:\\programdata\\anaconda3\\lib\\site-packages (from sklearn) (0.24.1)\n",
      "Requirement already satisfied: joblib>=0.11 in c:\\programdata\\anaconda3\\lib\\site-packages (from scikit-learn->sklearn) (1.0.1)\n",
      "Requirement already satisfied: scipy>=0.19.1 in c:\\programdata\\anaconda3\\lib\\site-packages (from scikit-learn->sklearn) (1.6.2)\n",
      "Requirement already satisfied: numpy>=1.13.3 in c:\\programdata\\anaconda3\\lib\\site-packages (from scikit-learn->sklearn) (1.20.1)\n",
      "Requirement already satisfied: threadpoolctl>=2.0.0 in c:\\programdata\\anaconda3\\lib\\site-packages (from scikit-learn->sklearn) (2.1.0)\n"
     ]
    }
   ],
   "source": [
    "!pip install sklearn"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {
    "ExecuteTime": {
     "end_time": "2021-09-18T15:40:29.264130Z",
     "start_time": "2021-09-18T15:40:28.924289Z"
    },
    "colab": {},
    "colab_type": "code",
    "id": "yr45GEzAOtq6"
   },
   "outputs": [],
   "source": [
    "import numpy as np\n",
    "import os\n",
    "\n",
    "# making random output stable\n",
    "np.random.seed(42)\n",
    "m = 60\n",
    "w1, w2 = 0.1, 0.3\n",
    "noise = 0.1\n",
    "\n",
    "# Create an array of the given shape \n",
    "# and populate it with random samples from a uniform distribution over [0, 1).\n",
    "angles = np.random.rand(m) * 3 * np.pi / 2 - 0.5\n",
    "# print('angles: {}\\nsize of dataset: {}'.format(angles, angles.shape))\n",
    "\n",
    "X = np.empty((m, 3))\n",
    "# print(X)\n",
    "X[:, 0] = np.cos(angles) + np.sin(angles)/2 + noise * np.random.randn(m) / 2\n",
    "X[:, 1] = np.sin(angles) * 0.7 + noise * np.random.randn(m) / 2\n",
    "X[:, 2] = X[:, 0] * w1 + X[:, 1] * w2 + noise * np.random.randn(m)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "colab_type": "text",
    "id": "ehCixfYWYgBc"
   },
   "source": [
    "## PCA using Scikit-Learn\n",
    "With Scikit-Learn, PCA is really trivial. It even takes care of mean centering for you, which has been perfectly packaged and easy to incorporate"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {
    "ExecuteTime": {
     "end_time": "2021-09-18T15:40:31.663542Z",
     "start_time": "2021-09-18T15:40:29.269502Z"
    },
    "colab": {},
    "colab_type": "code",
    "id": "d6tMz_zjY_Jz"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "pca = PCA(n_components = 0.99)\n",
      "\n",
      "First 5 Projection Points:\n",
      "[[ 0.690074    0.36150744]\n",
      " [-1.39636097 -0.34497714]\n",
      " [-1.00728461  0.35025708]\n",
      " [-0.2736333   0.50516373]\n",
      " [ 0.91324535 -0.26290852]]\n",
      "\n",
      "\n",
      "Principal Components:\n",
      "[[-0.95250178 -0.24902446 -0.17529172]\n",
      " [ 0.29267159 -0.9076305  -0.30091563]]\n",
      "\n",
      "\n",
      "Explained Variance Ratio:\n",
      "[0.85406025 0.13622918]\n",
      "\n"
     ]
    }
   ],
   "source": [
    "from sklearn.decomposition import PCA\n",
    "\n",
    "pca = PCA(n_components = 0.99)\n",
    "\n",
    "# you have to call 'fit' with appropriate arguments before using transrom method.\n",
    "# a = pca.fit(X)\n",
    "# b = pca.transform(X)\n",
    "# or simply use fit_transform()\n",
    "\n",
    "# 2-D\n",
    "X_decorrelated_DR = pca.fit_transform(X)\n",
    "X_decorrelated_DR[:] = X_decorrelated_DR[:]*(-1) \n",
    "\n",
    "# reconstruct dataset from X_decorrelated_DR\n",
    "X_reconstructed = pca.inverse_transform(X_decorrelated_DR)\n",
    "\n",
    "print(\"pca = PCA(n_components = 0.99)\\n\")\n",
    "print(\"First 5 Projection Points:\\n{}\\n\\n\".format(X_decorrelated_DR[:5]))\n",
    "print(\"Principal Components:\\n{}\\n\\n\".format(pca.components_))\n",
    "print(\"Explained Variance Ratio:\\n{}\\n\".format(pca.explained_variance_ratio_))\n",
    "# print('reconstructed dataset:\\n{}\\n'.format(X_reconstructed))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "colab_type": "text",
    "id": "YPJErcHIfeWp"
   },
   "source": [
    "***We can visibly tell that we can reduce our dimensions from 3 to 2 while preserving 99% of the variance***"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {
    "ExecuteTime": {
     "end_time": "2021-09-18T15:40:31.711397Z",
     "start_time": "2021-09-18T15:40:31.674284Z"
    },
    "colab": {
     "base_uri": "https://localhost:8080/",
     "height": 252
    },
    "colab_type": "code",
    "executionInfo": {
     "elapsed": 684,
     "status": "ok",
     "timestamp": 1546322325444,
     "user": {
      "displayName": "Bob Li",
      "photoUrl": "",
      "userId": "13034580972517925389"
     },
     "user_tz": -480
    },
    "id": "_jJIoUzlf6oa",
    "outputId": "45ed6c46-eb02-4d40-e324-eee30b51620f"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "pca = PCA(n_components = 0.999)\n",
      "\n",
      "First 5 Projection Points:\n",
      "[[-0.690074   -0.36150744  0.05176509]\n",
      " [ 1.39636097  0.34497714  0.04887253]\n",
      " [ 1.00728461 -0.35025708  0.04753837]\n",
      " [ 0.2736333  -0.50516373  0.13786693]\n",
      " [-0.91324535  0.26290852 -0.03027986]]\n",
      "\n",
      "\n",
      "Principal Components:\n",
      "[[-0.95250178 -0.24902446 -0.17529172]\n",
      " [ 0.29267159 -0.9076305  -0.30091563]\n",
      " [-0.08416476 -0.33792558  0.93740205]]\n",
      "\n",
      "\n",
      "Explained Variance Ratio:\n",
      "[0.85406025 0.13622918 0.00971057]\n",
      "\n"
     ]
    }
   ],
   "source": [
    "# preserving 99.9% of the variance\n",
    "pca = PCA(n_components = 0.999)\n",
    "\n",
    "# 3-D\n",
    "X_decorrelated = pca.fit_transform(X)\n",
    "\n",
    "print(\"pca = PCA(n_components = 0.999)\\n\")\n",
    "print(\"First 5 Projection Points:\\n{}\\n\\n\".format(X_decorrelated[:5]))\n",
    "print(\"Principal Components:\\n{}\\n\\n\".format(pca.components_))\n",
    "print(\"Explained Variance Ratio:\\n{}\\n\".format(pca.explained_variance_ratio_))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {
    "ExecuteTime": {
     "end_time": "2021-09-18T15:40:32.577605Z",
     "start_time": "2021-09-18T15:40:31.721159Z"
    },
    "colab": {},
    "colab_type": "code",
    "id": "zmUyRlSPmse_"
   },
   "outputs": [],
   "source": [
    "from matplotlib.patches import FancyArrowPatch\n",
    "from mpl_toolkits.mplot3d import proj3d\n",
    "\n",
    "class Arrow3D(FancyArrowPatch):\n",
    "    def __init__(self, xs, ys, zs, *args, **kwargs):\n",
    "        FancyArrowPatch.__init__(self, (0,0), (0,0), *args, **kwargs)\n",
    "        self._verts3d = xs, ys, zs\n",
    "\n",
    "    def draw(self, renderer):\n",
    "        xs3d, ys3d, zs3d = self._verts3d\n",
    "        xs, ys, zs = proj3d.proj_transform(xs3d, ys3d, zs3d, renderer.M)\n",
    "        self.set_positions((xs[0],ys[0]),(xs[1],ys[1]))\n",
    "        FancyArrowPatch.draw(self, renderer)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {
    "ExecuteTime": {
     "end_time": "2021-09-18T15:40:32.659633Z",
     "start_time": "2021-09-18T15:40:32.609343Z"
    },
    "colab": {
     "base_uri": "https://localhost:8080/",
     "height": 454
    },
    "colab_type": "code",
    "executionInfo": {
     "elapsed": 694,
     "status": "ok",
     "timestamp": 1546328037918,
     "user": {
      "displayName": "Bob Li",
      "photoUrl": "",
      "userId": "13034580972517925389"
     },
     "user_tz": -480
    },
    "id": "tQT8YSDxA1f8",
    "outputId": "3a43b0ba-0cb8-47e1-b019-2db84631fc6b"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Principal Components:\n",
      "[[-0.95250178 -0.24902446 -0.17529172]\n",
      " [ 0.29267159 -0.9076305  -0.30091563]\n",
      " [-0.08416476 -0.33792558  0.93740205]]\n",
      "\n",
      "\n",
      "Z value of the hyperplane:\n",
      "[[-0.63025233 -0.59433828 -0.55842423 -0.52251018 -0.48659612 -0.45068207\n",
      "  -0.41476802 -0.37885397 -0.34293992 -0.30702587]\n",
      " [-0.52611031 -0.49019626 -0.45428221 -0.41836815 -0.3824541  -0.34654005\n",
      "  -0.310626   -0.27471195 -0.2387979  -0.20288384]\n",
      " [-0.42196829 -0.38605424 -0.35014018 -0.31422613 -0.27831208 -0.24239803\n",
      "  -0.20648398 -0.17056993 -0.13465587 -0.09874182]\n",
      " [-0.31782626 -0.28191221 -0.24599816 -0.21008411 -0.17417006 -0.13825601\n",
      "  -0.10234196 -0.0664279  -0.03051385  0.0054002 ]\n",
      " [-0.21368424 -0.17777019 -0.14185614 -0.10594209 -0.07002804 -0.03411399\n",
      "   0.00180007  0.03771412  0.07362817  0.10954222]\n",
      " [-0.10954222 -0.07362817 -0.03771412 -0.00180007  0.03411399  0.07002804\n",
      "   0.10594209  0.14185614  0.17777019  0.21368424]\n",
      " [-0.0054002   0.03051385  0.0664279   0.10234196  0.13825601  0.17417006\n",
      "   0.21008411  0.24599816  0.28191221  0.31782626]\n",
      " [ 0.09874182  0.13465587  0.17056993  0.20648398  0.24239803  0.27831208\n",
      "   0.31422613  0.35014018  0.38605424  0.42196829]\n",
      " [ 0.20288384  0.2387979   0.27471195  0.310626    0.34654005  0.3824541\n",
      "   0.41836815  0.45428221  0.49019626  0.52611031]\n",
      " [ 0.30702587  0.34293992  0.37885397  0.41476802  0.45068207  0.48659612\n",
      "   0.52251018  0.55842423  0.59433828  0.63025233]]\n",
      "\n"
     ]
    }
   ],
   "source": [
    "axes = [-1.8, 1.8, -1.3, 1.3, -1.0, 1.0]\n",
    "\n",
    "x1s = np.linspace(axes[0], axes[1], 10)\n",
    "x2s = np.linspace(axes[2], axes[3], 10)\n",
    "x1, x2 = np.meshgrid(x1s, x2s)\n",
    "# principal components (orthogonal vectors that decide the hyperplane)\n",
    "C = pca.components_\n",
    "print(\"Principal Components:\\n{}\\n\\n\".format(C))\n",
    "# normal-vector of the hyperplane\n",
    "normal_vector = np.cross(C[0,:], C[1,:])\n",
    "\n",
    "# calculate z-value of the dataset\n",
    "z = np.empty((10, 10))\n",
    "for i in range(0,10):\n",
    "    for j in range(0,10):\n",
    "        z[i, j] = -(normal_vector[0]*x1[i, j]+normal_vector[1]*x2[i ,j])/normal_vector[2]\n",
    "\n",
    "print(\"Z value of the hyperplane:\\n{}\\n\".format(z))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {
    "ExecuteTime": {
     "end_time": "2021-09-18T15:40:34.718707Z",
     "start_time": "2021-09-18T15:40:32.664516Z"
    },
    "colab": {
     "base_uri": "https://localhost:8080/",
     "height": 357
    },
    "colab_type": "code",
    "executionInfo": {
     "elapsed": 1767,
     "status": "ok",
     "timestamp": 1546330698940,
     "user": {
      "displayName": "Bob Li",
      "photoUrl": "",
      "userId": "13034580972517925389"
     },
     "user_tz": -480
    },
    "id": "9BqcncFeKY7o",
    "outputId": "f72323ec-0f97-4219-c00a-c892e3b55912",
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 720x432 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "import matplotlib\n",
    "import matplotlib.pyplot as plt\n",
    "from mpl_toolkits.mplot3d import Axes3D\n",
    "\n",
    "# setup parameters of the coordinates\n",
    "fig = plt.figure(figsize=(10, 6))\n",
    "ax = fig.add_subplot(111, projection=\"3d\")    # first figure of 1*1 space\n",
    "ax.set_xlabel(\"$x_1$\", fontsize=18, color=\"tab:blue\")\n",
    "ax.set_ylabel(\"$x_2$\", fontsize=18, color=\"tab:blue\")\n",
    "ax.set_zlabel(\"$x_3$\", fontsize=18, color=\"tab:blue\")\n",
    "ax.set_xlim(axes[0:2])\n",
    "ax.set_ylim(axes[2:4])\n",
    "ax.set_zlim(axes[4:6])\n",
    "\n",
    "# plot the X_decorrelated_DR dataset\n",
    "k = 0\n",
    "Z_projected = np.empty((60, 1))\n",
    "for i in range(0,60):\n",
    "    for j in range(0,2):\n",
    "        while k<60:\n",
    "            Z_projected[k] = -(normal_vector[0]*X_decorrelated_DR[i, j]+normal_vector[1]*X_decorrelated_DR[i ,j])/normal_vector[2]\n",
    "            k = k+1\n",
    "\n",
    "# plot the original datatset\n",
    "ax.plot(X[:, 0], X[:, 1], X[:, 2], \"bo\", alpha=0.6)\n",
    "      \n",
    "# plot the X_decorrelated_DR dataset\n",
    "ax.plot(X_decorrelated_DR[:, 0], X_decorrelated_DR[:, 1], Z_projected[:, 0], \"mo\", alpha=0.6)\n",
    "\n",
    "# plot the track of projection\n",
    "for j in range(0, 60):\n",
    "    ax.add_artist(Arrow3D([X[j, 0], X_decorrelated_DR[j, 0]],[X[j, 1], X_decorrelated_DR[j, 1]],[X[j, 2], Z_projected[j, 0]], mutation_scale=15, lw=1, arrowstyle=\"-|>\", color=\"tab:gray\"))\n",
    "\n",
    "# plot the hyperplane(2_D) for projection\n",
    "ax.plot_surface(x1, x2, z, alpha=0.2, color=\"k\")\n",
    "\n",
    "# plot the principal compoents(orthogonal vectors that decide the hyperplane)\n",
    "ax.add_artist(Arrow3D([0, C[0, 0]],[0, C[0, 1]],[0, C[0, 2]], mutation_scale=15, lw=1, arrowstyle=\"-|>\", color=\"tab:red\"))\n",
    "ax.add_artist(Arrow3D([0, C[1, 0]],[0, C[1, 1]],[0, C[1, 2]], mutation_scale=15, lw=1, arrowstyle=\"-|>\", color=\"tab:red\"))\n",
    "\n",
    "# plot the origin\n",
    "ax.plot([0], [0], [0], \"r.\")\n",
    "\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "colab": {
   "collapsed_sections": [],
   "name": "dimensionality_reduction.ipynb",
   "provenance": [],
   "version": "0.3.2"
  },
  "hide_input": false,
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.8"
  },
  "toc": {
   "base_numbering": 1,
   "nav_menu": {},
   "number_sections": true,
   "sideBar": true,
   "skip_h1_title": false,
   "title_cell": "Table of Contents",
   "title_sidebar": "Contents",
   "toc_cell": false,
   "toc_position": {},
   "toc_section_display": true,
   "toc_window_display": false
  },
  "varInspector": {
   "cols": {
    "lenName": 16,
    "lenType": 16,
    "lenVar": 40
   },
   "kernels_config": {
    "python": {
     "delete_cmd_postfix": "",
     "delete_cmd_prefix": "del ",
     "library": "var_list.py",
     "varRefreshCmd": "print(var_dic_list())"
    },
    "r": {
     "delete_cmd_postfix": ") ",
     "delete_cmd_prefix": "rm(",
     "library": "var_list.r",
     "varRefreshCmd": "cat(var_dic_list()) "
    }
   },
   "types_to_exclude": [
    "module",
    "function",
    "builtin_function_or_method",
    "instance",
    "_Feature"
   ],
   "window_display": false
  }
 },
 "nbformat": 4,
 "nbformat_minor": 1
}
