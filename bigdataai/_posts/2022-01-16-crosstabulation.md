---
layout: single
title: "교차분석"
date: "2022-01-16 12:01:00 +0900"
last_modified_at: "2022-01-16 12:34:00 +0900"
---


# 1. 교차분석




```python
data <- read.csv("./data/cleanDescriptive.csv", header=TRUE)

```


```python
data
```


<table>
<thead><tr><th scope=col>resident</th><th scope=col>gender</th><th scope=col>age</th><th scope=col>level</th><th scope=col>cost</th><th scope=col>type</th><th scope=col>survey</th><th scope=col>pass</th><th scope=col>cost2</th><th scope=col>resident2</th><th scope=col>gender2</th><th scope=col>age2</th><th scope=col>level2</th><th scope=col>pass2</th></tr></thead>
<tbody>
	<tr><td> 1      </td><td>1       </td><td>50      </td><td> 1      </td><td>5.1     </td><td>1       </td><td> 1      </td><td> 2      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>54      </td><td> 2      </td><td>4.2     </td><td>1       </td><td> 2      </td><td> 2      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>대졸    </td><td>실패    </td></tr>
	<tr><td>NA      </td><td>1       </td><td>62      </td><td> 2      </td><td>4.7     </td><td>1       </td><td> 1      </td><td> 1      </td><td> 2      </td><td>NA      </td><td>남자    </td><td>노년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 4      </td><td>2       </td><td>50      </td><td>NA      </td><td>3.5     </td><td>1       </td><td> 4      </td><td> 1      </td><td>NA      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>NA      </td><td>합격    </td></tr>
	<tr><td> 5      </td><td>1       </td><td>51      </td><td> 1      </td><td>5.0     </td><td>1       </td><td> 3      </td><td> 1      </td><td> 2      </td><td>시구군  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 3      </td><td>1       </td><td>55      </td><td> 2      </td><td>5.4     </td><td>1       </td><td> 3      </td><td>NA      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>대졸    </td><td>NA      </td></tr>
	<tr><td> 2      </td><td>2       </td><td>56      </td><td> 1      </td><td>4.1     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td>NA      </td><td>1       </td><td>49      </td><td> 1      </td><td>4.4     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>NA      </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>49      </td><td> 2      </td><td>4.9     </td><td>1       </td><td> 1      </td><td> 1      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 5      </td><td>2       </td><td>49      </td><td>NA      </td><td>2.3     </td><td>1       </td><td> 2      </td><td> 1      </td><td> 1      </td><td>시구군  </td><td>여자    </td><td>장년층  </td><td>NA      </td><td>합격    </td></tr>
	<tr><td> 3      </td><td>1       </td><td>52      </td><td> 1      </td><td>4.2     </td><td>1       </td><td> 2      </td><td> 2      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>57      </td><td> 1      </td><td>6.7     </td><td>1       </td><td> 2      </td><td> 1      </td><td>NA      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 2      </td><td>2       </td><td>51      </td><td> 3      </td><td>4.3     </td><td>1       </td><td> 2      </td><td> 1      </td><td> 2      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>대학원졸</td><td>합격    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>63      </td><td> 1      </td><td>5.7     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>노년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 3      </td><td>1       </td><td>55      </td><td> 2      </td><td>4.6     </td><td>2       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>대졸    </td><td>실패    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>54      </td><td> 1      </td><td>5.1     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>48      </td><td> 2      </td><td>2.1     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 1      </td><td>특별시  </td><td>여자    </td><td>장년층  </td><td>대졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>57      </td><td> 1      </td><td>5.1     </td><td>2       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>49      </td><td>NA      </td><td>6.2     </td><td>2       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>NA      </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>65      </td><td> 1      </td><td>5.1     </td><td>1       </td><td> 2      </td><td> 1      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>노년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>49      </td><td> 1      </td><td>4.1     </td><td>1       </td><td> 2      </td><td> 1      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>53      </td><td> 2      </td><td>4.1     </td><td>1       </td><td> 1      </td><td> 1      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 2      </td><td>2       </td><td>45      </td><td> 3      </td><td>2.3     </td><td>1       </td><td> 3      </td><td> 1      </td><td> 1      </td><td>광역시  </td><td>여자    </td><td>중년층  </td><td>대학원졸</td><td>합격    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>55      </td><td> 1      </td><td>5.0     </td><td>1       </td><td> 3      </td><td> 1      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>60      </td><td> 2      </td><td>5.2     </td><td>2       </td><td> 2      </td><td> 1      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>노년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>48      </td><td> 1      </td><td>4.7     </td><td>2       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>특별시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>65      </td><td> 2      </td><td>4.4     </td><td>2       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>노년층  </td><td>대졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>55      </td><td> 1      </td><td>5.2     </td><td>2       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 2      </td><td>2       </td><td>48      </td><td> 1      </td><td>6.3     </td><td>1       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 5      </td><td>1       </td><td>63      </td><td> 3      </td><td>4.0     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>시구군  </td><td>남자    </td><td>노년층  </td><td>대학원졸</td><td>실패    </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td> 2      </td><td>2       </td><td>47      </td><td> 1      </td><td>5.4     </td><td>2       </td><td> 1      </td><td> 1      </td><td> 2      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 3      </td><td>2       </td><td>49      </td><td> 3      </td><td>7.0     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 3      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>대학원졸</td><td>실패    </td></tr>
	<tr><td> 4      </td><td>2       </td><td>64      </td><td> 2      </td><td>6.0     </td><td>1       </td><td> 2      </td><td> 1      </td><td> 2      </td><td>광역시  </td><td>여자    </td><td>노년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>65      </td><td> 3      </td><td>6.2     </td><td>1       </td><td> 3      </td><td> 1      </td><td>NA      </td><td>특별시  </td><td>남자    </td><td>노년층  </td><td>대학원졸</td><td>합격    </td></tr>
	<tr><td> 3      </td><td>2       </td><td>46      </td><td> 3      </td><td>6.1     </td><td>1       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>대학원졸</td><td>NA      </td></tr>
	<tr><td> 1      </td><td>2       </td><td>47      </td><td> 1      </td><td>5.0     </td><td>1       </td><td> 3      </td><td> 1      </td><td> 2      </td><td>특별시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>57      </td><td> 2      </td><td>5.8     </td><td>1       </td><td> 4      </td><td> 1      </td><td> 2      </td><td>특별시  </td><td>여자    </td><td>장년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 5      </td><td>1       </td><td>48      </td><td> 3      </td><td>4.0     </td><td>1       </td><td> 3      </td><td> 2      </td><td> 2      </td><td>시구군  </td><td>남자    </td><td>장년층  </td><td>대학원졸</td><td>실패    </td></tr>
	<tr><td> 3      </td><td>2       </td><td>58      </td><td> 1      </td><td>3.0     </td><td>1       </td><td> 4      </td><td> 2      </td><td> 1      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>65      </td><td> 1      </td><td>5.0     </td><td>1       </td><td> 3      </td><td> 1      </td><td> 2      </td><td>특별시  </td><td>여자    </td><td>노년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 5      </td><td>1       </td><td>55      </td><td> 2      </td><td>6.0     </td><td>1       </td><td> 4      </td><td> 1      </td><td> 2      </td><td>시구군  </td><td>남자    </td><td>장년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>61      </td><td>NA      </td><td>6.2     </td><td>2       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>특별시  </td><td>여자    </td><td>노년층  </td><td>NA      </td><td>실패    </td></tr>
	<tr><td> 5      </td><td>1       </td><td>49      </td><td> 3      </td><td>6.4     </td><td>2       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>시구군  </td><td>남자    </td><td>장년층  </td><td>대학원졸</td><td>실패    </td></tr>
	<tr><td> 5      </td><td>2       </td><td>45      </td><td> 3      </td><td>7.9     </td><td>1       </td><td> 2      </td><td> 1      </td><td> 3      </td><td>시구군  </td><td>여자    </td><td>중년층  </td><td>대학원졸</td><td>합격    </td></tr>
	<tr><td> 1      </td><td>1       </td><td>59      </td><td>NA      </td><td>6.4     </td><td>1       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>NA      </td><td>NA      </td></tr>
	<tr><td> 2      </td><td>2       </td><td>48      </td><td> 3      </td><td>6.3     </td><td>1       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>대학원졸</td><td>실패    </td></tr>
	<tr><td>NA      </td><td>1       </td><td>60      </td><td> 1      </td><td>6.1     </td><td>1       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>NA      </td><td>남자    </td><td>노년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 2      </td><td>2       </td><td>47      </td><td> 2      </td><td>6.3     </td><td>2       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>대졸    </td><td>실패    </td></tr>
	<tr><td> 4      </td><td>2       </td><td>49      </td><td> 1      </td><td>6.0     </td><td>2       </td><td> 1      </td><td> 1      </td><td> 2      </td><td>광역시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 2      </td><td>2       </td><td>63      </td><td> 1      </td><td>6.9     </td><td>1       </td><td> 2      </td><td> 1      </td><td>NA      </td><td>광역시  </td><td>여자    </td><td>노년층  </td><td>고졸    </td><td>합격    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>47      </td><td> 2      </td><td>6.2     </td><td>1       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>대졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>57      </td><td> 1      </td><td>5.9     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>특별시  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>58      </td><td> 2      </td><td>6.9     </td><td>1       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>특별시  </td><td>여자    </td><td>장년층  </td><td>대졸    </td><td>실패    </td></tr>
	<tr><td> 5      </td><td>2       </td><td>55      </td><td> 1      </td><td>7.7     </td><td>1       </td><td> 1      </td><td> 2      </td><td> 3      </td><td>시구군  </td><td>여자    </td><td>장년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>58      </td><td> 3      </td><td>6.3     </td><td>1       </td><td> 2      </td><td> 2      </td><td>NA      </td><td>특별시  </td><td>여자    </td><td>장년층  </td><td>대학원졸</td><td>실패    </td></tr>
	<tr><td> 1      </td><td>2       </td><td>62      </td><td> 2      </td><td>4.5     </td><td>1       </td><td> 3      </td><td> 1      </td><td> 2      </td><td>특별시  </td><td>여자    </td><td>노년층  </td><td>대졸    </td><td>합격    </td></tr>
	<tr><td> 2      </td><td>1       </td><td>65      </td><td> 1      </td><td>3.5     </td><td>1       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>광역시  </td><td>남자    </td><td>노년층  </td><td>고졸    </td><td>실패    </td></tr>
	<tr><td> 3      </td><td>1       </td><td>55      </td><td> 3      </td><td>6.1     </td><td>1       </td><td>NA      </td><td> 2      </td><td>NA      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>대학원졸</td><td>실패    </td></tr>
	<tr><td> 4      </td><td>1       </td><td>51      </td><td> 1      </td><td>5.5     </td><td>1       </td><td>NA      </td><td>NA      </td><td> 2      </td><td>광역시  </td><td>남자    </td><td>장년층  </td><td>고졸    </td><td>NA      </td></tr>
	<tr><td> 1      </td><td>1       </td><td>49      </td><td> 3      </td><td>4.4     </td><td>1       </td><td>NA      </td><td> 2      </td><td> 2      </td><td>특별시  </td><td>남자    </td><td>장년층  </td><td>대학원졸</td><td>실패    </td></tr>
</tbody>
</table>




```python
head(data)
```


<table>
<thead><tr><th scope=col>resident</th><th scope=col>gender</th><th scope=col>age</th><th scope=col>level</th><th scope=col>cost</th><th scope=col>type</th><th scope=col>survey</th><th scope=col>pass</th><th scope=col>cost2</th><th scope=col>resident2</th><th scope=col>gender2</th><th scope=col>age2</th><th scope=col>level2</th><th scope=col>pass2</th></tr></thead>
<tbody>
	<tr><td> 1    </td><td>1     </td><td>50    </td><td> 1    </td><td>5.1   </td><td>1     </td><td>1     </td><td> 2    </td><td> 2    </td><td>특별시</td><td>남자  </td><td>장년층</td><td>고졸  </td><td>실패  </td></tr>
	<tr><td> 2    </td><td>1     </td><td>54    </td><td> 2    </td><td>4.2   </td><td>1     </td><td>2     </td><td> 2    </td><td> 2    </td><td>광역시</td><td>남자  </td><td>장년층</td><td>대졸  </td><td>실패  </td></tr>
	<tr><td>NA    </td><td>1     </td><td>62    </td><td> 2    </td><td>4.7   </td><td>1     </td><td>1     </td><td> 1    </td><td> 2    </td><td>NA    </td><td>남자  </td><td>노년층</td><td>대졸  </td><td>합격  </td></tr>
	<tr><td> 4    </td><td>2     </td><td>50    </td><td>NA    </td><td>3.5   </td><td>1     </td><td>4     </td><td> 1    </td><td>NA    </td><td>광역시</td><td>여자  </td><td>장년층</td><td>NA    </td><td>합격  </td></tr>
	<tr><td> 5    </td><td>1     </td><td>51    </td><td> 1    </td><td>5.0   </td><td>1     </td><td>3     </td><td> 1    </td><td> 2    </td><td>시구군</td><td>남자  </td><td>장년층</td><td>고졸  </td><td>합격  </td></tr>
	<tr><td> 3    </td><td>1     </td><td>55    </td><td> 2    </td><td>5.4   </td><td>1     </td><td>3     </td><td>NA    </td><td> 2    </td><td>광역시</td><td>남자  </td><td>장년층</td><td>대졸  </td><td>NA    </td></tr>
</tbody>
</table>




```python
dim(data)
```



	<li>248</li>
	<li>14</li>





```python
# 변수 추출
x <- data$level2
y <- data$pass2
```


```python
# 데이터 프레임 생성
df <- data.frame(Level=x,Pass=y)
table(df$Level) # row
table(df$Pass) # column
```



        고졸     대졸 대학원졸
          93       86       57




    실패 합격
      96  139



```python
# 1) 교차분할표 생성
table(df) # 빈도보기
```


              Pass
    Level      실패 합격
      고졸       40   49
      대졸       27   55
      대학원졸   23   31



```python

# 2) package를 이용한 교차분할표 생성
install.packages("gmodels") # gmodels 패키지 설치
library(gmodels) # CrossTable() 함수 사용

CrossTable(x=df$Level, y=df$Pass)
```

    also installing the dependencies 'gtools', 'gdata'




      There is a binary version available but the source version is later:
           binary source needs_compilation
    gtools  3.8.2  3.9.2              TRUE

      Binaries will be installed
    package 'gtools' successfully unpacked and MD5 sums checked
    package 'gdata' successfully unpacked and MD5 sums checked
    package 'gmodels' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\RtmpgnDCWZ\downloaded_packages


    Warning message:
    "package 'gmodels' was built under R version 3.6.3"



       Cell Contents
    |-------------------------|
    |                       N |
    | Chi-square contribution |
    |           N / Row Total |
    |           N / Col Total |
    |         N / Table Total |
    |-------------------------|


    Total Observations in Table:  225


                 | df$Pass
        df$Level |      실패 |      합격 | Row Total |
    -------------|-----------|-----------|-----------|
            고졸 |        40 |        49 |        89 |
                 |     0.544 |     0.363 |           |
                 |     0.449 |     0.551 |     0.396 |
                 |     0.444 |     0.363 |           |
                 |     0.178 |     0.218 |           |
    -------------|-----------|-----------|-----------|
            대졸 |        27 |        55 |        82 |
                 |     1.026 |     0.684 |           |
                 |     0.329 |     0.671 |     0.364 |
                 |     0.300 |     0.407 |           |
                 |     0.120 |     0.244 |           |
    -------------|-----------|-----------|-----------|
        대학원졸 |        23 |        31 |        54 |
                 |     0.091 |     0.060 |           |
                 |     0.426 |     0.574 |     0.240 |
                 |     0.256 |     0.230 |           |
                 |     0.102 |     0.138 |           |
    -------------|-----------|-----------|-----------|
    Column Total |        90 |       135 |       225 |
                 |     0.400 |     0.600 |           |
    -------------|-----------|-----------|-----------|




# p-value(유의 확률, significance probability)


p-value는, 귀무가설(null hypothesis, H0)이 맞다는 전제 하에, 통계값(statistics)이 실제로 관측된 값 이상일 확률을 의미한다.

일반적으로 p-value는 어떤 가설을 전제로, 그 가설이 맞는다는 가정 하에, 내가 현재 구한 통계값이 얼마나 자주 나올 것인가, 를 의미한다고 할 수 있다. p-value는 가설검정이라는 것이 전체 데이터를 갖고 하는 것이 아닌 sampling 된 데이터를 갖고 하는 것이기 때문이다.

일반적으로 p-value < 0.05 혹은 0.01을 기준으로 합니다. 계산된 p-value가 기준값보다 작은 경우 귀무가설을 기각하는 것으로 즉, 극단적으로 귀무가설이 일어날 확률이 매우 낮은 상태를 의미합니다.

- 참고사이트 [https://blog.minitab.com/ko/understanding-statistics/what-can-you-say-when-your-p-value-is-greater-than-005](https://blog.minitab.com/ko/understanding-statistics/what-can-you-say-when-your-p-value-is-greater-than-005)

대다수의 분석에서는 0.05의 알파가 유의성의 기준으로 사용됩니다. P값이 0.05 미만인 경우 평균 간에 차이가 없다는 귀무 가설을 기각하고 유의한 차이가 있다는 결론을 내리게 됩니다. P값이 0.05보다 크면 큰 차이가 존재한다는 결론을 내릴 수 없습니다.

0.05 미만이면 유의하고(의미가 있다) 0.05를 초과하면 유의하지 않습니다.

(통계적 유의성은 모집단에 대한 가설이 가지는 통계적 의미를 말한다. 다시 말해서, 어떤 실험 결과 자료를 두고 "통계적으로 유의하다."라고 하는 것은 확률적으로 봐서 단순한 우연이라고 생각되지 않을 정도로 의미가 있다는 뜻이다. 반대로 "통계적으로 유의하지 않다."라고 하는 것은 실험 결과가 단순한 우연일 수도 있다는 뜻이다.)



- 귀무가설 이란?

- 참고사이트 [https://drhongdatanote.tistory.com/59](https://drhongdatanote.tistory.com/59)

모집단의 특성에 대해 옳다고 제안하는 잠정적인 주장입니다. 귀무가설은 "모집단의 모수는 00와 같다." 또는 "모집단의 모수는 00와 차이가 없다."라고 가정하는 것을 말합니다.

예를 들어 만약 전국 20세 이상의 평균 키가 170cm라는 주장을 통계적으로 검정한다면, 이에 대한 귀무 가설은 "20세 이상의 성인 남자의 평균 키는 170cm과 같다.(또는 차이가 없다.)"가 될 것입니다.
또 제약회사에서 개발한 신약의 효과를 검정한다면, 귀무가설은 "개발한 신약은 효과가 없다.(또는 차이가 없다.)"가 됩니다.

즉 귀무가설은 효과는 없다 또는 "~와 같다" 라는 형식으로 설정된다는 것을 알 수 있습니다.

## P값을 올바르게 해석하는 방법 How to Correctly Interpret P Values

### 참고사이트 - [https://blog.minitab.com/ko/adventures-in-statistics-2/how-to-correctly-interpret-p-values](https://blog.minitab.com/ko/adventures-in-statistics-2/how-to-correctly-interpret-p-values)

P값이 높은 경우: 귀무 가설이 참일 가능성이 높은 데이터입니다.

P값이 낮은 경우: 귀무 가설이 참일 가능성이 낮은 데이터입니다.


```python
# <실습> 부모의 학력수준이 자녀의 대학진학에 영향이 있는가를
#        알아보기 위해서 다음과 같이 교차분석을 수행하시오.
#<조건1> CrossTable()함수 이용
# 귀무가설 : 학년수준과 자녀의 대학진학여부와 관련이 없다.(O)
# 대립가설 : 학년수준과 자녀의 대학진학여부와 관련이 있다.(x)

# sample : 고졸(합격) : 49
# 기대값 식 = (셀의 행합 * 셀의 열합) / 전체합
value = (90 * 135) / 225
value # 54
# 기대비율 식 = (관측값 - 기대값)^2/ 기대값
rate = ((49 - p_value)^2) / p_value
rate # 0.462962962962963

```


54



0.462962962962963



```python
# 교차분할표 & 카이검정
CrossTable(x=df$Level, y=df$Pass, chisq = TRUE)
```



       Cell Contents
    |-------------------------|
    |                       N |
    | Chi-square contribution |
    |           N / Row Total |
    |           N / Col Total |
    |         N / Table Total |
    |-------------------------|


    Total Observations in Table:  225


                 | df$Pass
        df$Level |      실패 |      합격 | Row Total |
    -------------|-----------|-----------|-----------|
            고졸 |        40 |        49 |        89 |
                 |     0.544 |     0.363 |           |
                 |     0.449 |     0.551 |     0.396 |
                 |     0.444 |     0.363 |           |
                 |     0.178 |     0.218 |           |
    -------------|-----------|-----------|-----------|
            대졸 |        27 |        55 |        82 |
                 |     1.026 |     0.684 |           |
                 |     0.329 |     0.671 |     0.364 |
                 |     0.300 |     0.407 |           |
                 |     0.120 |     0.244 |           |
    -------------|-----------|-----------|-----------|
        대학원졸 |        23 |        31 |        54 |
                 |     0.091 |     0.060 |           |
                 |     0.426 |     0.574 |     0.240 |
                 |     0.256 |     0.230 |           |
                 |     0.102 |     0.138 |           |
    -------------|-----------|-----------|-----------|
    Column Total |        90 |       135 |       225 |
                 |     0.400 |     0.600 |           |
    -------------|-----------|-----------|-----------|


    Statistics for All Table Factors


    Pearson's Chi-squared test
    ------------------------------------------------------------
    Chi^2 =  2.766951     d.f. =  2     p =  0.2507057





# 결론 P값이 0.05 보다 크므로 실험 결과가 단순한 우연일 수도 있다는 뜻이고 귀무 가설이 참일 가능성이 높은 데이터입니다.

