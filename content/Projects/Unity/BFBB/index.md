---
title: "Beat Flavor: Breakfast Boogie"
date: 2024-03-21
summary: "Serve up rhythm, make an unbeatable flavor"
---

{{< github repo="lawrencemsteven/BFBB" >}}

<br>

{{< button href="https://github.com/lawrencemsteven/BFBB/releases/download/v2.0.0/v2.0.0.zip" target="_blank" >}}
Download {{< icon "download" >}}
{{< /button >}}

In Beat Flavor: Breakfast Boogie, players take on the role of a breakfast establishment employee who is responsible for managing and serving different customers inside the restaurant. Throughout their time managing the restaurant, the player will be able to make upgrades to their stations and add different designs to the restaurant. Everything that the player does must be done to the beat, from flipping pancakes to washing dishes.

{{< youtubeLite id="i6j2NTlXY-I" label="Beat Flavor: Breakfast Boogie Trailer" params="rel=0">}}

This project was made as part of a 12 person team for Junior design project 2 at Drexel. I was the producer on the team and I mostly managed the programming team as well as the documentation. Towards the end of our project I also worked on a lot of the polish work for the project such as the menus, screen transitions, and shop. 

{{< youtubeLite id="Zy2f6rTxe_k" label="Beat Flavor: Breakfast Boogie Gameplay" params="rel=0">}}

Probably the most important tool that I created for the team was an interpolation library called *Interpolawrence*. This was meant to give smooth or fast transitions to any zero to one value. I used this for our games camera transitions in the menu. It is also used on the individual menu transitions when the screens slide from top to bottom. Even the egg dancing animation is run using the same interpolating functions.

```C#
public class Interpolawrence : MonoBehaviour
{
    public enum InterpolawrenceSpeeds
    {
        Quick,
        Slow
    }

    public static float Lerp(InterpolawrenceSpeeds startSpeed, InterpolawrenceSpeeds stopSpeed, float amount)
    {
        if (startSpeed == InterpolawrenceSpeeds.Slow && stopSpeed == InterpolawrenceSpeeds.Slow)
        {
            float transitionAmount = Mathf.Lerp(0.0f, Mathf.PI, amount);
            return 0.5f * Mathf.Sin(transitionAmount - Mathf.PI / 2.0f) + 0.5f;
        }
        else if (startSpeed == InterpolawrenceSpeeds.Quick && stopSpeed == InterpolawrenceSpeeds.Quick)
        {
            float transitionAmount = Mathf.Lerp(0.0f, Mathf.PI, amount);
            if (transitionAmount < 0.5f)
            {
                return Mathf.Sin(transitionAmount) * 0.5f;
            }
            else
            {
                return Mathf.Abs(Mathf.Sin(transitionAmount) * 0.5f - 1.0f);
            }
        }
        else if (startSpeed == InterpolawrenceSpeeds.Slow && stopSpeed == InterpolawrenceSpeeds.Quick)
        {
            float transitionAmount = Mathf.Lerp(0.0f, Mathf.PI / 2.0f, amount);
            return Mathf.Sin(transitionAmount - Mathf.PI / 2.0f) + 1.0f;
        }
        else if (startSpeed == InterpolawrenceSpeeds.Quick && stopSpeed == InterpolawrenceSpeeds.Slow)
        {
            float transitionAmount = Mathf.Lerp(0.0f, Mathf.PI / 2.0f, amount);
            return Mathf.Sin(transitionAmount);
        }

        return -1.0f;
    }
}
```

Beat Flavor: Breakfast Boogie was definitely difficult to work on such a large team and within such a short time span being only 10 weeks. However, I really enjoyed working with this team and I am happy with our end result.