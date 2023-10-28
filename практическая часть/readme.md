Нейронка .Это лишь загатовки, модели у врагов в последствии будут другие (показ того, как реагируют два вида врага на игрока)
https://github.com/qune4q/Diplom/assets/50214016/232efd8e-6bcd-4af8-b451-106f27e2fec3
За основу к новой игре, взят один из моих прошлых проектов и его ИИ
Скрипт отвечающий за патрулирование местности: 
using UnityEngine.AI;
using System.Collections.Generic;
using UnityEngine;

public class PatrolBehaviour : StateMachineBehaviour
{
    float timer;
    List<Transform> points = new List<Transform>();
    NavMeshAgent agent;

    Transform player;
    float chaseRange = 10;
    // OnStateEnter is called when a transition starts and the state machine starts to evaluate this state
    override public void OnStateEnter(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        timer = 0;
        Transform pointsObject = GameObject.FindGameObjectWithTag("Points").transform; // Враг передвигается с заданной точки до другой точки, в то время ИИ иследует местность
        foreach (Transform t in pointsObject)
            points.Add(t);

        agent = animator.GetComponent<NavMeshAgent>(); // Обращение ИИ к анимации персонажа
        agent.SetDestination(points[0].position);

        player = GameObject.FindGameObjectWithTag("Player").transform; // ИИ пока патрулирует местность ищет на определенной дистанции игрока
    }

    // OnStateUpdate is called on each Update frame between OnStateEnter and OnStateExit callbacks
    override public void OnStateUpdate(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        if (agent.remainingDistance <= agent.stoppingDistance)
            agent.SetDestination(points[Random.Range(0, points.Count)].position);

        timer += Time.deltaTime;
        if (timer > 10)
            animator.SetBool("isPatrolling", false); // Если ИИ обнаружил игрока, то патрулирование отключается

        float distance = Vector3.Distance(animator.transform.position, player.position);
        if (distance < chaseRange)
            animator.SetBool("isChasing", true); // ИИ включает режим преследование игрока, и в дальнейших скриптах и атаковать игрока

    }

    // OnStateExit is called when a transition ends and the state machine finishes evaluating this state
    override public void OnStateExit(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        agent.SetDestination(agent.transform.position);
    }
}

Как и говорилось ранее, моя предыдущая игра была взята за основу к новой игре, т.к у них есть общие схожости в виде ИИ и главного персонажа:
zip файл игры находится в практической части.
