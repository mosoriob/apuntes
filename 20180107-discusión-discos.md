---


---

<h3 id="docker-y-volumes">Docker y volumes</h3>
<p>Contexto: discusión sobre el overhead (disco) que produce Docker cuando se lee/escribe en un volumen.</p>
<p>Considerando la <a href="https://github.com/moby/moby/issues/21485#issuecomment-250431595">discusión</a>, le da razón al amigo Sergio.
Como lo comenta el siguiente hombre:</p>
<blockquote>
<p>This issue resulted in #24307 (which fixes the odd regression between
1.5.0 packages). <strong>In addition, we discovered that the reason that Ubuntu doesn’t have this problem is because Ubuntu uses the deadline
IO scheduler by default (SUSE distributions use CFQ), which doesn’t
suffer as badly from the <em>blkio cgroup performance issue</em></strong></p>
</blockquote>
<p>Y parece tener razón, porque esto produce el commit del servicio
<a href="https://github.com/moby/moby/pull/24307">https://github.com/moby/moby/pull/24307</a></p>
<p>Como bien dice el commit:</p>
<blockquote>
<p>Having non-zero Limit*s causes performance problems due to accounting overhead</p>
</blockquote>
<p>Ahora me nació la siguiente pregunta, ¿es coherente medir afuera y dentro de container si siempre tenemos que pasar por el IO scheduler?</p>
<p>Buscando encontre la misma pregunta:</p>
<p><a href="https://github.com/moby/moby/issues/21485#issuecomment-250431595">https://github.com/moby/moby/issues/21485#issuecomment-250431595</a></p>
<blockquote>
<p>@mimizone According to the kernel guys at SUSE, the reason is that the CFQ IO scheduler will add latency to requests in order to make sure that two racing cgroups don’t starve one another. I don’t fully understand the code behind it, but that’s what they told me and the experimental results back this up (deadline scheduler would never dream of adding latency).</p>
</blockquote>
<p>De hecho en el comentario de arriba dice considerando <a href="https://www.thomas-krenn.com/en/wiki/Linux_Storage_Stack_Diagram#Diagram_for_Linux_Kernel_4.0">Linux Storage Stack Diagram</a>, da lo mismo medir afuera o dentro</p>
<p><img src="https://www.inf.utfsm.cl/~mosorio/fig.png" alt="enter image description here"></p>
<p>¿Entonces que es lo que puede cambiar? El algoritmo de IO scheduler y que el algoritmo tenga distitnas respuestas según la capa extra que tenemos cgroups</p>

