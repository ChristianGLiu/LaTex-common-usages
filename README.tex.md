\documentclass[]{report}
\usepackage{graphicx}
\usepackage{listings}
\lstset{language=Python}


% Title Page
\title{Assignment 4
	
	on
	
	CSCI 6610 Visual Analytics
}
\author{Christian Gang Liu (B000415613)}


\begin{document}
\maketitle

\begin{abstract}
	
{\em Important Notes: because I am principle programmer of our “outliers” group project implementation, so you might find similar coding on our project as well. }

\begin{enumerate}
\item Project technical used:
General speaking, the project is based on JS and Python 3.4+ as required by assignment 4 instruction.
\begin{itemize}
\item Flask: A python framework used for quick REST services establishment;
\item D3: A Javascript library used for generating the interactive ML graphic charts;
\item Plotly: A popular javascript library which based on D3 provides the complicated chart view (here I only used it for show the tabular table view on the page, this requirement is not asked by assignment, I used it only to demonstrate how data comes from backend framework (Flask);
\item Others: JQuery / Bootstrap (only for UI layout and event triggering) 
\item IDE: PyCharm;
\item AWS (extra work): public cloud deployment:\url{http://assignment4-dev.us-east-2.elasticbeanstalk.com/}
\end{itemize}

\item Project structure: 

\includegraphics[width=0.7\columnwidth]{img/project-structure}

As you can see above, I constructed project structure to comply with the AWS elastic sever standardization, in order to deploy on AWS for public access.

I utilized FLASK framework to quickly establish this lightweight project, like what I did for my group project as well (\url{http://flask-env.zsewpnnzda.us-east-2.elasticbeanstalk.com/}).

In order to simplify the assignment 4 review work. I planned to deploy it on AWS for quickly reviewing. Anyhow you can always open my project source folder to execute command: python application.py to exam it on local.

\item Execution: the execution instruction is also mentioned in the README file under project folder.

\item General UI introduction (the details will be described specifically to each of questions of assignment 4):

\includegraphics[width=0.7\columnwidth]{img/ui-layout}

\begin{itemize}
\item Left Menu: they are four different tables which are required by assignment 4:
\begin{itemize}
\item Iris
\item Wine
\item Mtcars
\item Assignment 1 dataset (bonus)
\end{itemize}
\item Main Menu:
\begin{itemize}
\item Tabular view on Html page (extra work): it shows tabular view directly from JSON response of Flask REST services:
\item Radviz: The assignment 2 functionality for each of tables;
\item Correlation Matrix: Heatmap of each table;
\item Clustering: there will be a comparison between original labels (classification) and clustering by K-means, as well as a measurement score showing on the page.
\item Notes: the Bonus requirement of assignment 1 dataset will share the same functionalities.
\end{itemize}
\end{itemize}

\end{enumerate}

\end{abstract}

\textbf{The specific answers to the assignment 4 questions:}
\begin{description}
\item[1]
{\em [10 Marks] Create a backend that will provide the data and metadata that can be used to display the visualization
a.	Use an HTTP request to retrieve data from the back-end and use it to generate the visualization on the front-end.
b.	Tip: You can return, along with the data, some metadata like column names or other information that could be useful to handle and/or display the data in the front-end.}

\textbf{answer:}
Front-end ( JS retrievals JSON data, sends to html page):

\includegraphics[width=0.7\columnwidth]{img/q1-1}
\bigbreak
Back-end Flask WSGI endpoint (REST services) – Code snippets:

\includegraphics[width=0.7\columnwidth]{img/q1-2}

\includegraphics[width=0.7\columnwidth]{img/q1-3}
\bigbreak
The returning JSON sample like this: ($http://assignment4-dev.us-east-2.elasticbeanstalk.com/read\_iris$)

\includegraphics[width=0.7\columnwidth]{img/q1-4}
\bigbreak
The list of REST services showing below (hostname is http://assignment4-dev.us-east- 2.elasticbeanstalk.com):
\begin{itemize}
\item application.route('/')

\item application.route('/read\_iris')

\item application.route('/read\_iris\_correlation')

\item application.route('/read\_iris\_clustering')

\item application.route('/read\_wine')

\item application.route('/read\_wine\_correlation')

\item application.route('/read\_wine\_clustering')

\item application.route('/read\_car')

\item application.route('/read\_car\_correlation')

\item application.route('/read\_car\_clustering')

\item application.route('/read\_assignment1')

\item application.route('/read\_assignment1\_correlation')

\item application.route('/read\_assignment1\_clustering')

\item application.route('/show\_table')


\end{itemize}


\item[2]
{\em [20 Marks] Add an option on the interface to choose a different dataset (iris or winequality)
a.	The backend will return the new dataset}

\textbf{Answer:}

Front-end: when you click any option in left panel, the back-end will return corresponding response:

\includegraphics[width=0.7\columnwidth]{img/q2-1}
\bigbreak
\textbf{Back-end returning:}

\begin{itemize}
\item IRIS: http://assignment4-dev.us-east-2.elasticbeanstalk.com/read\_iris

\item WINE: http://assignment4-dev.us-east-2.elasticbeanstalk.com/read\_wine 

\item MTCARS: http://assignment4-dev.us-east-2.elasticbeanstalk.com/read\_car

\item ASSIGNMENT1: http://assignment4-dev.us-east-2.elasticbeanstalk.com/read\_assignment1

\end{itemize}

{\em b.	You can keep a state on the front-end and send it on every request to identify the current dataset in use.}

I am using the request parameter $ /?data=[dataset\_name]$ to identify current dataset using:

Like $ /?data=[iris\|wine\|assignment1\|car] $
\item[3]
{\em 3.	[20 Marks] When hovering an instance of a given cluster, show (as a tooltip or in other available space) the correlation matrix for instances of that cluster.
	a.	The correlation matrix should be calculated and returned by the back-end.
}


\textbf{Answer:}

Front-end:

\includegraphics[width=0.7\columnwidth]{img/q3-1}

Back-end:

\begin{lstlisting}[frame=single]
@application.route('/read_wine_correlation') 
def read_wine_correlation():
return jsonify(data.read_wine_correlation())

\end{lstlisting}

So whenever you click “correlation matrix” tab, it will send REST call like(giving an example): 


\begin{lstlisting}[frame=single]
def read_wine_correlation() 
-> readAssignment4Data: 
rawData = getAssignment4Data().wine correlation_result = rawData.corr()
return correlation_result.to_json(orient='records')

\end{lstlisting}

{\em b.	This should be displayed on the front-end through a color matrix. See example of such matrix below (Tip: feel free to use libraries to help you):}

So I utilize the corr() function of pandas to generate correlation heatmap

\item[4]
{\em [40 Marks] Implement a button that requests the backend to clusterize the data using one of:
	K-Means or DBScan
	a.	You should color the instances using the clustering information
}


\textbf{Answer:}

\includegraphics[width=0.7\columnwidth]{img/q4-1}

{\em b.	Add a switch button to choose between the color modes: cluster colors or class-based colors.}

\textbf{Answer:}

\includegraphics[width=0.7\columnwidth]{img/q4-2}
\bigbreak

After we switch back to actual classification:

\includegraphics[width=0.7\columnwidth]{img/q4-3}
\bigbreak
{\em c.	The clusterization should be performed on the same dataset currently seem in the visualization.}

\textbf{Answer:}

Since $/?data=[dataset\_name]$ remembers the current dataset. Clustering tab will go further to call 
\begin{itemize}
\item @application.route('/read\_iris\_clustering') 
\item @application.route('/read\_wine\_clustering') 
\item @application.route('/read\_car\_clustering') 
\item @application.route('/read\_assignment1\_clustering')
\end{itemize}
Page will show corresponding result to current dataset:

\includegraphics[width=0.7\columnwidth]{img/q4-4}
\bigbreak
\includegraphics[width=0.7\columnwidth]{img/q4-5}
\bigbreak
\includegraphics[width=0.7\columnwidth]{img/q4-6}
\bigbreak
\includegraphics[width=0.7\columnwidth]{img/q4-7}
\bigbreak
{\em d.	You just need to implement for one clusterization algorithm.}

\textbf{Answer:}

K-MEAN

{\em e.	You may use existing implementations of the clustering algorithms.}

\textbf{Answer:}

SKLearn.cluster.kmean

\item[5]
{\em [10 Marks] Add one (or more) options to configure the parameters of the clustering algorithm
	a.	Clicking the button should make a new clusterization with the new parameters and update the colors on the visualization.
}


\textbf{Answer:}

\end{document}          
