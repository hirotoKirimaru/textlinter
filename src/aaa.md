  const handleMouseDown = (event: React.MouseEvent<HTMLDivElement>) => {
    const initialPos = { x: event.pageX, y: event.pageY };
    let diff = { x: 0, y: 0 }
    const handleMouseMove = (event: MouseEvent) => {
      const { pageX, pageY } = event;
      diff = { x: pageX - initialPos.x, y: pageY - initialPos.y };
      ref.current.style.transform = `translateX(${diff.x}px) translateY(${diff.y}px)`;
    };
    const handleMouseUp = () => {
      window.removeEventListener("mousemove", handleMouseMove);
      window.removeEventListener("mouseup", handleMouseUp);
      move(diff.y, diff.x)
      ref.current.style.transform = "";
    };
    window.addEventListener("mousemove", handleMouseMove);
    window.addEventListener("mouseup", handleMouseUp);
  };